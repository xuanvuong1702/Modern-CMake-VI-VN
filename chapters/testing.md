# Kiểm thử

## Thông tin kiểm thử chung

Trong tệp CMakeLists.txt chính của bạn, bạn cần thêm lệnh gọi hàm sau (không nằm trong thư mục con):

```cmake
if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
    include(CTest)
endif()
```

Điều này sẽ bật kiểm thử và đặt tùy chọn `BUILD_TESTING` để người dùng có thể bật và tắt kiểm thử (cùng với [một số thứ khác](https://gitlab.kitware.com/cmake/cmake/blob/master/Modules/CTest.cmake)). Hoặc bạn có thể tự mình làm điều này bằng cách gọi trực tiếp `enable_testing()`.

Khi bạn thêm thư mục kiểm thử của mình, bạn nên làm điều gì đó như thế này:

```cmake
if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME AND BUILD_TESTING)
    add_subdirectory(tests)
endif()
```

Lý do cho điều này là nếu người khác bao gồm gói của bạn và họ sử dụng `BUILD_TESTING`, họ có thể không muốn các kiểm thử của bạn được xây dựng. Trong trường hợp hiếm hoi mà bạn thực sự muốn bật kiểm thử trên cả hai gói, bạn có thể cung cấp một ghi đè:

```cmake
if((CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME OR MYPROJECT_BUILD_TESTING) AND BUILD_TESTING)
    add_subdirectory(tests)
endif()
```

Trường hợp sử dụng chính cho ghi đè ở trên thực sự nằm trong các ví dụ của chính cuốn sách này, vì dự án CMake chính thực sự muốn chạy tất cả các kiểm thử dự án con.

Bạn có thể đăng ký mục tiêu bằng:

```cmake
add_test(NAME TestName COMMAND TargetName)
```

Nếu bạn đặt một thứ gì đó ngoài tên mục tiêu sau COMMAND, nó sẽ được đăng ký dưới dạng dòng lệnh để chạy. Việc đặt biểu thức trình tạo cũng sẽ hợp lệ:

```cmake
add_test(NAME TestName COMMAND $<TARGET_FILE:${TESTNAME}>)
```

điều này sẽ sử dụng vị trí đầu ra (do đó, tệp thực thi) của mục tiêu được tạo.

## Xây dựng như một phần của kiểm thử

Nếu bạn muốn chạy CMake để xây dựng một dự án như một phần của kiểm thử, bạn cũng có thể làm điều đó (trên thực tế, đây là cách CMake tự kiểm thử). Ví dụ: nếu dự án chính của bạn được gọi là `MyProject` và bạn có một dự án `examples/simple` có thể tự xây dựng, điều này sẽ giống như:

```cmake
add_test(
  NAME
    ExampleCMakeBuild
  COMMAND
    "${CMAKE_CTEST_COMMAND}"
             --build-and-test "${My_SOURCE_DIR}/examples/simple"
                              "${CMAKE_CURRENT_BINARY_DIR}/simple"
             --build-generator "${CMAKE_GENERATOR}"
             --test-command "${CMAKE_CTEST_COMMAND}"
)
```

## Khung kiểm thử

Hãy xem các chương phụ để biết công thức nấu ăn cho các khung phổ biến.

- [GoogleTest](testing/googletest.md): Một tùy chọn phổ biến từ Google. Việc phát triển có thể hơi chậm.
- [Catch2](testing/catch.md): Một khung hiện đại, giống PyTest với các macro thông minh.
- [DocTest](https://github.com/onqtam/doctest): Một sự thay thế cho Catch2 được cho là biên dịch nhanh hơn nhiều và gọn gàng hơn. Xem chương Catch2 và thay thế bằng DocTest.


