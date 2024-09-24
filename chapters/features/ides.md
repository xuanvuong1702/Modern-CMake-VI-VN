# Hỗ trợ IDE

Nhìn chung, các IDE đã được hỗ trợ bởi một dự án CMake tiêu chuẩn. Chỉ có một vài điều bổ sung có thể giúp IDE hoạt động tốt hơn nữa.

## Thư mục cho các mục tiêu

Một số IDE, như Xcode, hỗ trợ thư mục. Bạn phải bật thủ công thuộc tính toàn cục `USE_FOLDERS` để cho phép CMake sắp xếp các tệp của bạn theo thư mục:

```cmake
set_property(GLOBAL PROPERTY USE_FOLDERS ON)
```

Sau đó, bạn có thể thêm các mục tiêu vào thư mục sau khi bạn tạo chúng:

```cmake
set_property(TARGET MyFile PROPERTY FOLDER "Scripts")
```

Các thư mục có thể được lồng nhau bằng `/`.

Bạn có thể kiểm soát cách các tệp hiển thị trong mỗi thư mục bằng các biểu thức chính quy hoặc danh sách rõ ràng trong [`source_group`](https://cmake.org/cmake/help/latest/command/source_group.html):

## Thư mục cho các tệp

Bạn cũng có thể kiểm soát cách các thư mục bên trong mục tiêu xuất hiện. Có hai cách, cả hai đều sử dụng lệnh `source_group`. Cách truyền thống là

```cmake
source_group("Source Files\\New Directory" REGULAR_EXPRESSION ".*\\.c[ucp]p?")
```

Bạn có thể liệt kê rõ ràng các tệp bằng `FILES` hoặc sử dụng `REGULAR_EXPRESSION`. Bằng cách này, bạn có toàn quyền kiểm soát cấu trúc thư mục. Tuy nhiên, nếu bố cục trên đĩa của bạn được thiết kế tốt, bạn có thể chỉ muốn bắt chước điều đó. Trong CMake 3.8+, bạn có thể làm như vậy rất dễ dàng với phiên bản mới của lệnh `source_group`:

```cmake
source_group(TREE "${CMAKE_CURRENT_SOURCE_DIR}/base/dir" PREFIX "Header Files" FILES ${FILE_LIST})
```

Đối với tùy chọn `TREE`, bạn thường nên cung cấp đường dẫn đầy đủ bắt đầu bằng một cái gì đó giống như `${CMAKE_CURRENT_SOURCE_DIR}/` (vì lệnh diễn giải các đường dẫn tương đối so với thư mục bản dựng).
Tiền tố cho bạn biết nơi nó đặt nó vào cấu trúc IDE và tùy chọn `FILES` nhận một danh sách các tệp.
CMake sẽ loại bỏ đường dẫn `TREE` khỏi đường dẫn `FILE_LIST`, nó sẽ thêm `PREFIX` và đó sẽ là cấu trúc thư mục IDE.

> Lưu ý: Nếu bạn cần hỗ trợ CMake < 3.8, tôi khuyên bạn chỉ nên bảo vệ lệnh trên và chỉ hỗ trợ bố cục thư mục đẹp trên CMake 3.8+. Đối với các phương pháp cũ hơn để thực hiện bố cục thư mục này, hãy xem
> [bài đăng trên blog này][sorting].

## Chạy với IDE

Để sử dụng IDE, hãy chuyển `-G"tên của IDE"` nếu CMake có thể tạo các tệp của IDE đó (như Xcode, Visual Studio) hoặc mở tệp CMakeLists.txt từ IDE của bạn nếu IDE đó có hỗ trợ tích hợp sẵn cho CMake (CLion, QtCreator, và nhiều IDE khác).

[sorting]: http://blog.audio-tk.com/2015/09/01/sorting-source-files-and-projects-in-folders-with-cmake-and-visual-studioxcode/