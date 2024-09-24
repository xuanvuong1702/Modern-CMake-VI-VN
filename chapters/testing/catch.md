## Catch

[Catch2] (phiên bản chỉ dành cho C++11) là một giải pháp kiểm thử mạnh mẽ, giống như PyTest dành cho Python. Nó hỗ trợ nhiều trình biên dịch hơn GTest và nhanh chóng hỗ trợ những thứ mới, như bản build M1 trên macOS. Nó cũng có một phiên bản nhỏ hơn nhưng nhanh hơn, [doctest](https://github.com/onqtam/doctest), được biên dịch nhanh nhưng thiếu các tính năng như matchers. Để sử dụng Catch trong một dự án CMake, có một số tùy chọn.

### Phương thức cấu hình

Catch có hỗ trợ CMake tốt, mặc dù để sử dụng nó, bạn cần kho lưu trữ đầy đủ. Điều này có thể được thực hiện với submodule hoặc FetchContent. Cả hai ví dụ [`extended-project`](https://gitlab.com/CLIUtils/modern-cmake/-/tree/master/examples/extended-project) và [`fetch`](https://gitlab.com/CLIUtils/modern-cmake/-/tree/master/examples/fetch) đều sử dụng FetchContent. Xem [tài liệu](https://github.com/catchorg/Catch2/blob/v2.x/docs/cmake-integration.md#top).

### Tải xuống nhanh

Đây có thể là phương pháp đơn giản nhất và hỗ trợ các phiên bản CMake cũ hơn. Bạn có thể tải xuống tệp tiêu đề tất cả trong một trong một bước:

```cmake
add_library(catch_main main.cpp)
target_include_directories(catch_main PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}")
set(url https://github.com/philsquared/Catch/releases/download/v2.13.6/catch.hpp)
file(
  DOWNLOAD ${url} "${CMAKE_CURRENT_BINARY_DIR}/catch.hpp"
  STATUS status
  EXPECTED_HASH SHA256=681e7505a50887c9085539e5135794fc8f66d8e5de28eadf13a30978627b0f47)
list(GET status 0 error)
if(error)
  message(FATAL_ERROR "Không thể tải xuống ${url}")
endif()
target_include_directories(catch_main PUBLIC "${CMAKE_CURRENT_BINARY_DIR}")
```

Điều này sẽ tải xuống hai lần khi Catch 3 được phát hành, vì bây giờ nó yêu cầu hai tệp (nhưng bạn không còn phải viết main.cpp nữa). Tệp `main.cpp` trông như thế này:

```cpp
#define CATCH_CONFIG_MAIN
#include "catch.hpp"
```

### Vendoring

Nếu bạn chỉ cần thả bản phát hành include đơn lẻ của Catch vào dự án của mình, đây là những gì bạn cần để thêm Catch:

```cmake
# Chuẩn bị thư viện "Catch" cho các tệp thực thi khác
set(CATCH_INCLUDE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/extern/catch)
add_library(Catch2::Catch IMPORTED INTERFACE)
set_property(Catch2::Catch PROPERTY INTERFACE_INCLUDE_DIRECTORIES "${CATCH_INCLUDE_DIR}")
```

Sau đó, bạn sẽ liên kết đến Catch2::Catch. Điều này sẽ ổn với tư cách là target INTERFACE vì bạn sẽ không xuất các bài kiểm tra của mình.

### Include trực tiếp

Nếu bạn thêm thư viện bằng ExternalProject, FetchContent hoặc git submodule, bạn cũng có thể `add_subdirectory` Catch (CMake 3.1+).

Catch cũng cung cấp hai mô-đun CMake mà bạn có thể sử dụng để đăng ký các bài kiểm tra riêng lẻ với CMake.

[catch2]: https://github.com/catchorg/Catch2


**Giải thích:**

Catch2 là một framework kiểm thử đơn vị dành cho C++. Tài liệu này mô tả các cách khác nhau để tích hợp Catch2 vào dự án CMake của bạn.

**Các phương thức cấu hình:**

- **FetchContent hoặc Submodule:** Tải xuống toàn bộ kho lưu trữ Catch2 bằng FetchContent hoặc Git submodule.
- **Tải xuống nhanh:** Tải xuống tệp tiêu đề "tất cả trong một" của Catch2.
- **Vendoring:** Sao chép tệp tiêu đề Catch2 vào dự án của bạn.
- **Include trực tiếp:** Thêm thư mục Catch2 vào dự án của bạn và sử dụng `add_subdirectory`.

**Chi tiết về từng phương thức:**

- **FetchContent/Submodule:** Ưu điểm là bạn luôn có phiên bản Catch2 mới nhất. Nhược điểm là cần phải tải xuống toàn bộ kho lưu trữ.
- **Tải xuống nhanh:** Ưu điểm là đơn giản và nhanh chóng. Nhược điểm là bạn phải tự cập nhật tệp tiêu đề khi có phiên bản mới.
- **Vendoring:** Ưu điểm là bạn kiểm soát hoàn toàn phiên bản Catch2 được sử dụng. Nhược điểm là bạn phải tự cập nhật tệp tiêu đề.
- **Include trực tiếp:** Ưu điểm là bạn có thể sử dụng các mô-đun CMake của Catch2 để đăng ký các bài kiểm tra. Nhược điểm là bạn phải thêm thư mục Catch2 vào dự án của mình.

**Tóm tắt:**

Tài liệu này cung cấp nhiều tùy chọn để tích hợp Catch2 vào dự án CMake của bạn. Bạn có thể chọn phương pháp phù hợp nhất với nhu cầu của mình. 


