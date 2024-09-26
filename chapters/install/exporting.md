# Xuất

:::{danger}

Hành vi mặc định của việc xuất đã thay đổi trong CMake 3.15. Vì việc thay đổi các tệp trong thư mục chính của người dùng được coi là "bất ngờ" (và đúng là vậy, đó là lý do tại sao chương này tồn tại), nên nó không còn là hành vi mặc định nữa. Nếu bạn đặt phiên bản CMake tối thiểu hoặc tối đa là 3.15 trở lên, điều này sẽ không còn xảy ra nữa trừ khi bạn đặt `CMAKE_EXPORT_PACKAGE_REGISTRY` như được hiển thị bên dưới.

:::

Có ba cách để truy cập một dự án từ một dự án khác: thư mục con, thư mục bản dựng được xuất và cài đặt. Để sử dụng thư mục bản dựng của một dự án trong một dự án khác, bạn sẽ cần xuất các đích. Việc xuất các đích là cần thiết cho một cài đặt đúng cách; cho phép sử dụng thư mục bản dựng chỉ là thêm vào hai dòng. Nói chung, đây không phải là cách làm việc mà tôi khuyến nghị, nhưng có thể hữu ích cho việc phát triển và như một cách để chuẩn bị quy trình cài đặt được thảo luận sau.

Bạn nên tạo một tập hợp xuất, có thể gần cuối tệp `CMakeLists.txt` chính của bạn:

```cmake
export(TARGETS MyLib1 MyLib2 NAMESPACE MyLib:: FILE MyLibTargets.cmake)
```

Điều này đặt các đích bạn đã liệt kê vào một tệp trong thư mục bản dựng và tùy chọn thêm tiền tố cho chúng bằng một không gian tên. Giờ đây, để cho phép CMake tìm thấy gói này, hãy xuất gói vào thư mục `$HOME/.cmake/packages`:

```cmake
set(CMAKE_EXPORT_PACKAGE_REGISTRY ON)
export(PACKAGE MyLib)
```

Giờ đây, nếu bạn `find_package(MyLib)`, CMake có thể tìm thấy thư mục bản dựng. Hãy xem tệp `MyLibTargets.cmake` được tạo để giúp bạn hiểu chính xác những gì được tạo; nó chỉ là một tệp CMake thông thường, với các đích được xuất.

Lưu ý rằng có một nhược điểm: nếu bạn đã nhập các phụ thuộc, chúng sẽ cần được nhập trước khi bạn `find_package`. Điều đó sẽ được khắc phục trong phương pháp tiếp theo. 
