# Chạy các chương trình khác

## Chạy một lệnh tại thời điểm cấu hình

Chạy một lệnh tại thời điểm cấu hình tương đối dễ. Sử dụng [`execute_process`][execute_process] để chạy một tiến trình và truy cập kết quả. Nói chung, nên tránh mã hóa cứng đường dẫn chương trình vào CMake của bạn; bạn có thể sử dụng `${CMAKE_COMMAND}`, `find_package(Git)` hoặc `find_program` để truy cập vào một lệnh để chạy. Sử dụng `RESULT_VARIABLE` để kiểm tra mã trả về và `OUTPUT_VARIABLE` để nhận đầu ra.

Dưới đây là một ví dụ cập nhật tất cả các mô-đun con git:

```cmake
find_package(Git QUIET)

if(GIT_FOUND AND EXISTS "${PROJECT_SOURCE_DIR}/.git")
    execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
                    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
                    RESULT_VARIABLE GIT_SUBMOD_RESULT)
    if(NOT GIT_SUBMOD_RESULT EQUAL "0")
        message(FATAL_ERROR "git submodule update --init --recursive failed with ${GIT_SUBMOD_RESULT}, please checkout submodules")
    endif()
endif()
```

## Chạy một lệnh tại thời điểm xây dựng

Các lệnh thời điểm xây dựng phức tạp hơn một chút. Sự phức tạp chính đến từ hệ thống đích; khi nào bạn muốn lệnh của mình chạy? Nó có tạo ra đầu ra mà mục tiêu khác cần không? Với ý nghĩ đó, đây là một ví dụ gọi một tập lệnh Python để tạo tệp tiêu đề:

```cmake
find_package(PythonInterp REQUIRED)
add_custom_command(OUTPUT "${CMAKE_CURRENT_BINARY_DIR}/include/Generated.hpp"
    COMMAND "${PYTHON_EXECUTABLE}" "${CMAKE_CURRENT_SOURCE_DIR}/scripts/GenerateHeader.py" --argument
    DEPENDS some_target)

add_custom_target(generate_header ALL
    DEPENDS "${CMAKE_CURRENT_BINARY_DIR}/include/Generated.hpp")

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/include/Generated.hpp DESTINATION include)
```

Ở đây, việc tạo xảy ra sau khi `some_target` hoàn thành và xảy ra khi bạn chạy make mà không có mục tiêu (`ALL`). Nếu bạn biến điều này thành phần phụ thuộc của một mục tiêu khác với `add_dependencies`, bạn có thể tránh từ khóa `ALL`. Hoặc, bạn có thể yêu cầu người dùng tạo rõ ràng mục tiêu `generate_header` khi tạo.

## Các tiện ích phổ biến được bao gồm

Một công cụ hữu ích trong việc viết các bản dựng CMake hoạt động đa nền tảng là `cmake -E <mode>` (được thấy trong các tệp CMake dưới dạng `${CMAKE_COMMAND} -E`). Chế độ này cho phép CMake thực hiện nhiều việc khác nhau mà không cần gọi rõ ràng các công cụ hệ thống, như `copy`, `make_directory` và `remove`. Nó chủ yếu được sử dụng cho các lệnh thời điểm xây dựng. Lưu ý rằng chế độ `create_symlink` rất hữu ích trước đây chỉ dành cho Unix, nhưng đã được thêm vào cho Windows trong CMake 3.13. [Xem tài liệu](https://cmake.org/cmake/help/latest/manual/cmake.1.html#command-line-tool-mode).

[execute_process]: https://cmake.org/cmake/help/latest/command/execute_process.html
