# Chạy các chương trình khác

## Chạy một lệnh tại thời điểm cấu hình

Chạy một lệnh tại thời điểm cấu hình tương đối dễ dàng. Sử dụng [`execute_process`][execute_process] để chạy một tiến trình và truy cập kết quả. Nói chung, bạn nên tránh mã hóa cứng đường dẫn của chương trình vào CMake của bạn; bạn có thể sử dụng `${CMAKE_COMMAND}`, `find_package(Git)` hoặc `find_program` để truy cập vào một lệnh để chạy. Sử dụng `RESULT_VARIABLE` để kiểm tra mã trả về và `OUTPUT_VARIABLE` để lấy đầu ra.

Dưới đây là một ví dụ cập nhật tất cả các git submodule:

```cmake
find_package(Git QUIET)

if(GIT_FOUND AND EXISTS "${PROJECT_SOURCE_DIR}/.git")
    execute_process(COMMAND ${GIT_EXECUTABLE} submodule update --init --recursive
                    WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
                    RESULT_VARIABLE GIT_SUBMOD_RESULT)
    if(NOT GIT_SUBMOD_RESULT EQUAL "0")
        message(FATAL_ERROR "git submodule update --init --recursive failed with ${GIT_SUBMOD_RESULT}, vui lòng checkout các submodule")
    endif()
endif()
```

## Chạy một lệnh tại thời điểm build

Các lệnh thời điểm build phức tạp hơn một chút. Sự phức tạp chính đến từ hệ thống đích; khi nào bạn muốn lệnh của mình chạy? Nó có tạo ra đầu ra mà mục tiêu khác cần không? Với ý nghĩ đó, đây là một ví dụ gọi một tập lệnh Python để tạo tệp header:

```cmake
find_package(PythonInterp REQUIRED)
add_custom_command(OUTPUT "${CMAKE_CURRENT_BINARY_DIR}/include/Generated.hpp"
    COMMAND "${PYTHON_EXECUTABLE}" "${CMAKE_CURRENT_SOURCE_DIR}/scripts/GenerateHeader.py" --argument
    DEPENDS some_target)

add_custom_target(generate_header ALL
    DEPENDS "${CMAKE_CURRENT_BINARY_DIR}/include/Generated.hpp")

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/include/Generated.hpp DESTINATION include)
```

Ở đây, việc tạo ra xảy ra sau khi `some_target` hoàn thành và xảy ra khi bạn chạy make mà không có mục tiêu (`ALL`). Nếu bạn biến điều này thành một dependency của một mục tiêu khác với `add_dependencies`, bạn có thể tránh từ khóa `ALL`. Hoặc, bạn có thể yêu cầu người dùng build rõ ràng mục tiêu `generate_header` khi thực hiện make.

## Các tiện ích phổ biến được include

Một công cụ hữu ích trong việc viết các bản build CMake hoạt động đa nền tảng là `cmake -E <mode>` (được thấy trong các tệp CMake dưới dạng `${CMAKE_COMMAND} -E`). Chế độ này cho phép CMake thực hiện nhiều việc khác nhau mà không cần gọi rõ ràng các công cụ hệ thống, như `copy`, `make_directory` và `remove`. Nó chủ yếu được sử dụng cho các lệnh thời điểm build. Lưu ý rằng chế độ `create_symlink` rất hữu ích trước đây chỉ dành cho Unix, nhưng đã được thêm vào cho Windows trong CMake 3.13. [Xem tài liệu](https://cmake.org/cmake/help/latest/manual/cmake.1.html#command-line-tool-mode).

[execute_process]: https://cmake.org/cmake/help/latest/command/execute_process.html
