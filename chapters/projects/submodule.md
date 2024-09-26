## Phương pháp Git Submodule

Nếu bạn muốn thêm một kho lưu trữ Git trên cùng một dịch vụ (GitHub, GitLab, BitBucket, v.v.), thì lệnh Git sau đây là lệnh chính xác để thiết lập nó như một submodule trong thư mục `extern`:

```bash
$ git submodule add ../../owner/repo.git extern/repo
```

Đường dẫn tương đối đến kho lưu trữ rất quan trọng; nó cho phép bạn giữ cùng phương thức truy cập (ssh hoặc https) như kho lưu trữ cha. Điều này hoạt động rất tốt trong hầu hết các trường hợp. Khi bạn ở bên trong submodule, bạn có thể coi nó giống như một kho lưu trữ bình thường, và khi bạn ở trong kho lưu trữ cha, bạn có thể "thêm" để thay đổi con trỏ commit hiện tại.

Nhưng nhược điểm truyền thống là bạn phải để người dùng của mình biết các lệnh git submodule, để họ có thể `init` và `update` kho lưu trữ, hoặc họ phải thêm `--recursive` khi họ clone kho lưu trữ của bạn ban đầu. CMake có thể đưa ra một giải pháp:

```cmake
find_package(Git QUIET)
if(GIT_FOUND AND EXISTS "${PROJECT_SOURCE_DIR}/.git")
# Cập nhật submodule khi cần
    option(GIT_SUBMODULE "Kiểm tra submodule trong quá trình build" ON)
    if(GIT_SUBMODULE)
        message(STATUS "Cập nhật submodule")
        execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
                        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
                        RESULT_VARIABLE GIT_SUBMOD_RESULT)
        if(NOT GIT_SUBMOD_RESULT EQUAL "0")
            message(FATAL_ERROR "git submodule update --init --recursive failed with ${GIT_SUBMOD_RESULT}, vui lòng checkout các submodule")
        endif()
    endif()
endif()

if(NOT EXISTS "${PROJECT_SOURCE_DIR}/extern/repo/CMakeLists.txt")
    message(FATAL_ERROR "Các submodule chưa được tải xuống! GIT_SUBMODULE đã bị tắt hoặc không thành công. Vui lòng cập nhật các submodule và thử lại.")
endif()
```

Dòng đầu tiên kiểm tra Git bằng CMake tích hợp sẵn `FindGit.cmake`. Sau đó, nếu bạn đang ở trong một bản checkout git của mã nguồn, hãy thêm một tùy chọn (mặc định là `ON`) cho phép nhà phát triển tắt tính năng này nếu họ cần. Sau đó, chúng tôi chạy lệnh để lấy tất cả các kho lưu trữ và thông báo lỗi nếu lệnh đó không thành công, kèm theo một thông báo lỗi rõ ràng. Cuối cùng, chúng tôi xác minh rằng các kho lưu trữ tồn tại trước khi tiếp tục, bất kể phương pháp nào được sử dụng để lấy chúng. Bạn có thể sử dụng `OR` để liệt kê một số.

Bây giờ, người dùng của bạn có thể hoàn toàn không biết đến sự tồn tại của các submodule, và bạn vẫn có thể duy trì các thực tiễn phát triển tốt! Điều duy nhất cần lưu ý là đối với các nhà phát triển; bạn sẽ reset submodule khi bạn chạy lại CMake nếu bạn đang phát triển bên trong submodule. Chỉ cần thêm các commit mới vào vùng staging của kho lưu trữ cha và bạn sẽ ổn.

Sau đó, bạn có thể include các dự án cung cấp hỗ trợ CMake tốt:

```cmake
add_subdirectory(extern/repo)
```

Hoặc, bạn có thể tự build một thư viện giao diện nếu đó là một dự án chỉ có header. Hoặc, bạn có thể sử dụng `find_package` nếu điều đó được hỗ trợ, có thể chuẩn bị thư mục tìm kiếm ban đầu là thư mục bạn đã thêm (kiểm tra tài liệu hoặc tệp `Find*.cmake` mà bạn đang sử dụng). Bạn cũng có thể include một thư mục tệp trợ giúp CMake nếu bạn thêm vào `CMAKE_MODULE_PATH` của mình, ví dụ: để thêm các tệp `FindPython*.cmake` được cải thiện của `pybind11`.

## Phần thưởng: Số phiên bản Git

Di chuyển phần này sang phần Git:

```cmake
execute_process(COMMAND ${GIT_EXECUTABLE} rev-parse --short HEAD
                WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}"
                OUTPUT_VARIABLE PACKAGE_GIT_VERSION
                ERROR_QUIET
                OUTPUT_STRIP_TRAILING_WHITESPACE)
```