## Giao tiếp với mã của bạn

## Tệp Cấu hình

CMake cho phép bạn truy cập các biến CMake từ mã của mình bằng cách sử dụng `configure_file`. Lệnh này sao chép một tệp (thường kết thúc bằng `.in`) từ nơi này sang nơi khác, thay thế tất cả các biến CMake mà nó tìm thấy. Nếu bạn muốn tránh thay thế cú pháp `${}` hiện có trong tệp đầu vào, hãy sử dụng từ khóa `@ONLY`. Ngoài ra còn có một từ khóa `COPY_ONLY` nếu bạn chỉ sử dụng nó như một sự thay thế cho `file(COPY)`.

Chức năng này được sử dụng khá thường xuyên; ví dụ, trên `Version.h.in`:

### Version.h.in

```cpp
#pragma once

#define MY_VERSION_MAJOR @PROJECT_VERSION_MAJOR@
#define MY_VERSION_MINOR @PROJECT_VERSION_MINOR@
#define MY_VERSION_PATCH @PROJECT_VERSION_PATCH@
#define MY_VERSION_TWEAK @PROJECT_VERSION_TWEAK@
#define MY_VERSION "@PROJECT_VERSION@"
```

### Dòng CMake:

```cmake
configure_file (
    "${PROJECT_SOURCE_DIR}/include/My/Version.h.in"
    "${PROJECT_BINARY_DIR}/include/My/Version.h"
)
```

Bạn nên bao gồm thư mục include nhị phân khi xây dựng dự án của mình. Nếu bạn muốn đặt bất kỳ biến đúng/sai nào trong tiêu đề, CMake có các thay thế `#cmakedefine` và `#cmakedefine01` dành riêng cho C để tạo các dòng định nghĩa phù hợp.

Bạn cũng có thể (và thường làm) sử dụng điều này để tạo ra các tệp `.cmake`, chẳng hạn như các tệp cấu hình (xem [cài đặt](https://cliutils.gitlab.io/modern-cmake/chapters/install/installing.html)).

## Đọc tệp

Hướng ngược lại cũng có thể được thực hiện; bạn có thể đọc một cái gì đó (như một phiên bản) từ các tệp nguồn của mình. Ví dụ: nếu bạn có một thư viện chỉ dành cho tiêu đề mà bạn muốn cung cấp có hoặc không có CMake, thì đây sẽ là cách tốt nhất để xử lý một phiên bản. Điều này sẽ trông giống như sau:

```cmake
# Giả sử phiên bản chính thức được liệt kê trong một dòng duy nhất
# Điều này sẽ có nhiều phần nếu chọn từ MAJOR, MINOR, v.v.
set(VERSION_REGEX "#define MY_VERSION[ \t]+\"(.+)\"")

# Đọc dòng chứa phiên bản
file(STRINGS "${CMAKE_CURRENT_SOURCE_DIR}/include/My/Version.hpp"
    VERSION_STRING REGEX ${VERSION_REGEX})

# Chọn ra chỉ phiên bản
string(REGEX REPLACE ${VERSION_REGEX} "\\1" VERSION_STRING "${VERSION_STRING}")

# Tự động nhận PROJECT_VERSION_MAJOR, My_VERSION_MAJOR, v.v.
project(My LANGUAGES CXX VERSION ${VERSION_STRING})
```

Ở trên, `file(STRINGS file_name variable_name REGEX regex)` chọn các dòng khớp với một regex; và cùng một regex được sử dụng để sau đó chọn ra nhóm chụp dấu ngoặc đơn với phần phiên bản. Replace được sử dụng với thay thế ngược để chỉ xuất ra nhóm đó. 
