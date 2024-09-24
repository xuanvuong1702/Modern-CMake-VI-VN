## FetchContent (CMake 3.11+)

Thông thường, bạn muốn tải xuống dữ liệu hoặc gói như một phần của quá trình cấu hình thay vì quá trình build. Điều này đã được phát minh ra nhiều lần trong các mô-đun của bên thứ ba, nhưng cuối cùng đã được thêm vào chính CMake như một phần của CMake 3.11 với tên gọi là mô-đun [FetchContent].

Mô-đun [FetchContent] có tài liệu tuyệt vời mà tôi sẽ không cố gắng lặp lại. Các ý tưởng chính là:

- Sử dụng `FetchContent_Declare(MyName)` để lấy dữ liệu hoặc một gói. Bạn có thể đặt URL, kho lưu trữ Git và hơn thế nữa.
- Sử dụng `FetchContent_GetProperties(MyName)` trên tên bạn đã chọn trong bước đầu tiên để lấy các biến `MyName_*`.
- Kiểm tra `MyName_POPULATED` và nếu chưa được điền, hãy sử dụng `FetchContent_Populate(MyName)` (và nếu là một gói, hãy sử dụng `add_subdirectory("${MyName_SOURCE_DIR}" "${MyName_BINARY_DIR}")`)

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

**Giải thích:**

`FetchContent` là một mô-đun trong CMake (từ phiên bản 3.11) cho phép bạn tải xuống và sử dụng các dự án hoặc dữ liệu bên ngoài trong quá trình cấu hình dự án của bạn. 

**Các bước sử dụng FetchContent:**

1. **Khai báo:** Sử dụng `FetchContent_Declare` để khai báo dự án hoặc dữ liệu cần tải xuống. Bạn có thể chỉ định URL, kho lưu trữ Git, phiên bản, v.v.
2. **Lấy thuộc tính:** Sử dụng `FetchContent_GetProperties` để lấy các biến chứa thông tin về dự án đã khai báo (ví dụ: đường dẫn nguồn, đường dẫn build).
3. **Kiểm tra và tải xuống:** Kiểm tra biến `MyName_POPULATED` để xem dự án đã được tải xuống chưa. Nếu chưa, hãy sử dụng `FetchContent_Populate` để tải xuống.
4. **Thêm vào dự án:** Nếu dự án được tải xuống là một dự án CMake, hãy sử dụng `add_subdirectory` để thêm nó vào dự án của bạn.

**Ví dụ:**

Đoạn mã ví dụ cho thấy cách tải xuống và sử dụng thư viện Catch2 bằng FetchContent.

**CMake 3.14+:**

- `FetchContent_MakeAvailable` được sử dụng để tải xuống và thêm Catch2 vào dự án một cách tự động.

**CMake 3.11+:**

- Cần kiểm tra `catch_POPULATED` và sử dụng `FetchContent_Populate` và `add_subdirectory` thủ công.

**Macro `FetchContent_MakeAvailable`:**

- Macro này được định nghĩa để mô phỏng cú pháp của `FetchContent_MakeAvailable` trong CMake 3.14+ cho các phiên bản CMake cũ hơn (3.11+).

**Tóm lại:**

`FetchContent` là một công cụ mạnh mẽ để quản lý các dependency bên ngoài trong CMake. Nó giúp đơn giản hóa quá trình tải xuống, cấu hình và tích hợp các dự án bên ngoài vào dự án của bạn.
