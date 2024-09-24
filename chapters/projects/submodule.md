## Phương pháp Git Submodule

Nếu bạn muốn thêm một kho lưu trữ Git trên cùng một dịch vụ (GitHub, GitLab, BitBucket, v.v.), thì lệnh Git sau đây là lệnh chính xác để thiết lập nó như một submodule trong thư mục `extern`:

```bash
$ git submodule add ../../owner/repo.git extern/repo
```

Đường dẫn tương đối đến kho lưu trữ rất quan trọng; nó cho phép bạn giữ cùng phương thức truy cập (ssh hoặc https) như kho lưu trữ cha. Điều này hoạt động rất tốt trong hầu hết các trường hợp. Khi bạn ở bên trong submodule, bạn có thể coi nó giống như một kho lưu trữ bình thường và khi bạn ở trong kho lưu trữ cha, bạn có thể "thêm" để thay đổi con trỏ commit hiện tại.

Nhưng nhược điểm truyền thống là bạn phải để người dùng của mình biết các lệnh git submodule, để họ có thể `init` và `update` kho lưu trữ, hoặc họ phải thêm `--recursive` khi họ clone kho lưu trữ của bạn ban đầu. CMake có thể đưa ra giải pháp:

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
            message(FATAL_ERROR "git submodule update --init --recursive failed with ${GIT_SUBMOD_RESULT}, vui lòng checkout submodule")
        endif()
    endif()
endif()

if(NOT EXISTS "${PROJECT_SOURCE_DIR}/extern/repo/CMakeLists.txt")
    message(FATAL_ERROR "Submodule chưa được tải xuống! GIT_SUBMODULE đã bị tắt hoặc không thành công. Vui lòng cập nhật submodule và thử lại.")
endif()
```

Dòng đầu tiên kiểm tra Git bằng CMake tích hợp sẵn `FindGit.cmake`. Sau đó, nếu bạn đang ở trong một bản checkout git của nguồn của mình, hãy thêm một tùy chọn (mặc định là `ON`) cho phép nhà phát triển tắt tính năng này nếu họ cần. Sau đó, chúng tôi chạy lệnh để lấy tất cả các kho lưu trữ và không thành công nếu lệnh đó không thành công, với một thông báo lỗi đẹp. Cuối cùng, chúng tôi xác minh rằng các kho lưu trữ tồn tại trước khi tiếp tục, bất kể phương pháp nào được sử dụng để lấy chúng. Bạn có thể sử dụng `OR` để liệt kê một số.

Bây giờ, người dùng của bạn có thể hoàn toàn không biết đến sự tồn tại của các submodule và bạn vẫn có thể duy trì các phương pháp phát triển tốt! Điều duy nhất cần lưu ý là đối với các nhà phát triển; bạn sẽ đặt lại submodule khi bạn chạy lại CMake nếu bạn đang phát triển bên trong submodule. Chỉ cần thêm các commit mới vào khu vực tổ chức cha mẹ và bạn sẽ ổn.

Sau đó, bạn có thể bao gồm các dự án cung cấp hỗ trợ CMake tốt:

```cmake
add_subdirectory(extern/repo)
```

Hoặc, bạn có thể tự build một target thư viện giao diện nếu đó là một dự án chỉ dành cho tiêu đề. Hoặc, bạn có thể sử dụng `find_package` nếu điều đó được hỗ trợ, có thể chuẩn bị thư mục tìm kiếm ban đầu là thư mục bạn đã thêm (kiểm tra tài liệu hoặc tệp cho tệp `Find*.cmake` bạn đang sử dụng). Bạn cũng có thể bao gồm một thư mục tệp trợ giúp CMake nếu bạn nối vào `CMAKE_MODULE_PATH` của mình, ví dụ: để thêm các tệp `FindPython*.cmake` được cải thiện của `pybind11`.

## Phần thưởng: Số phiên bản Git

Di chuyển phần này sang phần Git:

```cmake
execute_process(COMMAND ${GIT_EXECUTABLE} rev-parse --short HEAD
                WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}"
                OUTPUT_VARIABLE PACKAGE_GIT_VERSION
                ERROR_QUIET
                OUTPUT_STRIP_TRAILING_WHITESPACE)
```


**Giải thích:**

Tài liệu này hướng dẫn cách sử dụng Git submodule trong dự án CMake để quản lý các dependency bên ngoài.

**Git submodule là gì?**

Git submodule cho phép bạn nhúng một kho lưu trữ Git khác vào trong kho lưu trữ Git của bạn. Điều này hữu ích khi bạn muốn sử dụng mã nguồn từ một dự án khác trong dự án của mình mà không cần sao chép trực tiếp mã nguồn đó.

**Cách sử dụng Git submodule trong CMake:**

1. **Thêm submodule:** Sử dụng lệnh `git submodule add` để thêm kho lưu trữ Git bên ngoài vào dự án của bạn.
2. **Cập nhật submodule:** Sử dụng đoạn mã CMake được cung cấp để tự động cập nhật submodule trong quá trình build. Đoạn mã này kiểm tra xem Git có được cài đặt hay không, sau đó chạy lệnh `git submodule update --init --recursive` để cập nhật tất cả submodule.
3. **Kiểm tra submodule:** Đoạn mã CMake cũng kiểm tra xem submodule đã được tải xuống hay chưa trước khi tiếp tục build.
4. **Thêm submodule vào dự án:** Sử dụng `add_subdirectory` để thêm submodule vào dự án CMake của bạn.
5. **Lấy số phiên bản Git:** Đoạn mã CMake cuối cùng cho thấy cách lấy số phiên bản Git của dự án và lưu nó vào biến `PACKAGE_GIT_VERSION`.

**Ưu điểm của việc sử dụng Git submodule:**

- Quản lý dependency dễ dàng hơn.
- Giữ cho mã nguồn của bạn gọn gàng và ngăn nắp.
- Dễ dàng cập nhật dependency lên phiên bản mới nhất.

**Lưu ý:**

- Nhà phát triển cần lưu ý rằng việc chạy lại CMake khi đang phát triển bên trong submodule sẽ đặt lại submodule.
- Bạn có thể sử dụng `find_package` hoặc include các tệp trợ giúp CMake từ submodule để tích hợp submodule vào dự án của bạn.


