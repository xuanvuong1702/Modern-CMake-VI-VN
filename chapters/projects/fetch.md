## FetchContent (CMake 3.11+)

Thông thường, bạn muốn thực hiện việc tải xuống dữ liệu hoặc các gói như một phần của quá trình cấu hình thay vì quá trình build. Điều này đã được phát minh ra nhiều lần trong các mô-đun bên thứ ba, nhưng cuối cùng đã được thêm vào chính CMake như một phần của CMake 3.11 với tên gọi là mô-đun [FetchContent].

Mô-đun [FetchContent] có tài liệu tuyệt vời mà tôi sẽ không cố gắng lặp lại. Các ý tưởng chính là:

- Sử dụng `FetchContent_Declare(MyName)` để lấy dữ liệu hoặc một gói. Bạn có thể đặt URL, kho lưu trữ Git và hơn thế nữa.
- Sử dụng `FetchContent_GetProperties(MyName)` trên tên bạn đã chọn trong bước đầu tiên để lấy các biến `MyName_*`.
- Kiểm tra `MyName_POPULATED`, và nếu chưa được khởi tạo, hãy sử dụng `FetchContent_Populate(MyName)` (và nếu là một gói, hãy sử dụng `add_subdirectory("${MyName_SOURCE_DIR}" "${MyName_BINARY_DIR}")`)

Ví dụ: để tải xuống Catch2:

```cmake
FetchContent_Declare(
  catch
  GIT_REPOSITORY https://github.com/catchorg/Catch2.git
  GIT_TAG        v2.13.6
)

# CMake 3.14+
FetchContent_MakeAvailable(catch)
```

Nếu bạn không thể sử dụng CMake 3.14+, cách cổ điển để chuẩn bị mã là:

```cmake
# CMake 3.11+
FetchContent_GetProperties(catch)
if(NOT catch_POPULATED)
  FetchContent_Populate(catch)
  add_subdirectory(${catch_SOURCE_DIR} ${catch_BINARY_DIR})
endif()
```

Tất nhiên, bạn có thể gói gọn điều này thành một macro:

```cmake
if(${CMAKE_VERSION} VERSION_LESS 3.14)
    macro(FetchContent_MakeAvailable NAME)
        FetchContent_GetProperties(${NAME})
        if(NOT ${NAME}_POPULATED)
    	    FetchContent_Populate(${NAME})
    	    add_subdirectory(${${NAME}_SOURCE_DIR} ${${NAME}_BINARY_DIR})
        endif()
    endmacro()
endif()
```

Bây giờ bạn đã có cú pháp CMake 3.14+ trong CMake 3.11+.

Xem ví dụ [tại đây](https://gitlab.com/CLIUtils/modern-cmake/-/tree/master/examples/fetch).

[fetchcontent]: https://cmake.org/cmake/help/latest/module/FetchContent.html