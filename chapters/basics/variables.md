# Biến và Cache

## Biến cục bộ

Chúng ta sẽ đề cập đến các biến trước. Một biến cục bộ được thiết lập như sau:

```CMake
set(MY_VARIABLE "value")
```

Tên của các biến thường được viết hoa toàn bộ và giá trị theo sau. Bạn truy cập một biến bằng cách sử dụng `${}`, chẳng hạn như `${MY_VARIABLE}`. [^1] CMake có khái niệm về phạm vi; bạn có thể truy cập giá trị của biến sau khi bạn thiết lập nó miễn là bạn đang ở trong cùng một phạm vi. Nếu bạn rời khỏi một hàm hoặc một tệp trong một thư mục con, biến sẽ không còn được xác định nữa. Bạn có thể thiết lập một biến trong phạm vi ngay phía trên phạm vi hiện tại của bạn với `PARENT_SCOPE` ở cuối.

Danh sách chỉ đơn giản là một loạt các giá trị khi bạn thiết lập chúng:

```cmake
set(MY_LIST "one" "two")
```

mà nội bộ trở thành các giá trị được phân tách bằng `;`. Vì vậy, đây là một câu lệnh giống hệt nhau:

```cmake
set(MY_LIST "one;two")
```

Lệnh `list()` có các tiện ích để làm việc với danh sách và `separate_arguments` sẽ biến một chuỗi được phân tách bằng dấu cách thành một danh sách (tại chỗ). Lưu ý rằng một giá trị không được trích dẫn trong CMake giống như một giá trị được trích dẫn nếu không có dấu cách trong đó; điều này cho phép bạn bỏ qua các dấu ngoặc kép hầu hết thời gian khi làm việc với giá trị mà bạn biết không thể chứa dấu cách.

Khi một biến được mở rộng bằng cách sử dụng cú pháp `${}`, tất cả các quy tắc tương tự về dấu cách đều được áp dụng. Đặc biệt cẩn thận với các đường dẫn; đường dẫn có thể chứa dấu cách bất cứ lúc nào và phải luôn được trích dẫn khi chúng là một biến (không bao giờ viết `${MY_PATH}`, luôn phải là `"${MY_PATH}"`).

## Biến Cache

Nếu bạn muốn thiết lập một biến từ dòng lệnh, CMake cung cấp một bộ nhớ cache biến. Một số biến đã có ở đây, như `CMAKE_BUILD_TYPE`. Cú pháp để khai báo một biến và thiết lập nó nếu nó chưa được thiết lập là:

```cmake
set(MY_CACHE_VARIABLE "VALUE" CACHE STRING "Description")
```

Điều này sẽ **không** thay thế một giá trị hiện có. Điều này là để bạn có thể thiết lập những thứ này trên dòng lệnh và không bị ghi đè khi tệp CMake thực thi. Nếu bạn muốn sử dụng các biến này như một biến toàn cục tạm thời, thì bạn có thể làm:

```cmake
set(MY_CACHE_VARIABLE "VALUE" CACHE STRING "" FORCE)
mark_as_advanced(MY_CACHE_VARIABLE)
```

Dòng đầu tiên sẽ khiến giá trị được thiết lập cho dù thế nào đi nữa và dòng thứ hai sẽ giữ cho biến không xuất hiện trong danh sách các biến nếu bạn chạy `cmake -L ..` hoặc sử dụng GUI. Điều này rất phổ biến, bạn cũng có thể sử dụng kiểu `INTERNAL` để làm điều tương tự (mặc dù về mặt kỹ thuật, nó buộc kiểu STRING, điều này sẽ không ảnh hưởng đến bất kỳ mã CMake nào phụ thuộc vào biến):

```cmake
set(MY_CACHE_VARIABLE "VALUE" CACHE INTERNAL "")
```

Vì `BOOL` là một kiểu biến rất phổ biến, bạn có thể thiết lập nó ngắn gọn hơn với phím tắt:

```cmake
option(MY_OPTION "This is settable from the command line" OFF)
```

Đối với kiểu dữ liệu `BOOL`, có một số cách diễn đạt khác nhau cho `ON` và `OFF`.

Xem [cmake-variables] để biết danh sách các biến đã biết trong CMake.

## Biến môi trường

Bạn cũng có thể `set(ENV{variable_name} value)` và nhận các biến môi trường `$ENV{variable_name}`, mặc dù nói chung, nên tránh chúng.

## Cache

Cache thực chất chỉ là một tệp văn bản, `CMakeCache.txt`, được tạo trong thư mục bản dựng khi bạn chạy CMake. Đây là cách CMake ghi nhớ bất cứ thứ gì bạn thiết lập, vì vậy bạn không phải liệt kê lại các tùy chọn của mình mỗi khi bạn chạy lại CMake.

## Thuộc tính

Cách khác mà CMake lưu trữ thông tin là trong các thuộc tính. Điều này giống như một biến, nhưng nó được gắn vào một số mục khác, như một thư mục hoặc một mục tiêu. Một thuộc tính toàn cục có thể là một biến toàn cục không được lưu trong bộ nhớ cache hữu ích. Nhiều thuộc tính mục tiêu được khởi tạo từ một biến phù hợp với `CMAKE_` ở phía trước. Ví dụ: việc thiết lập `CMAKE_CXX_STANDARD` sẽ có nghĩa là tất cả các mục tiêu mới được tạo sẽ có `CXX_STANDARD` được thiết lập thành giá trị đó khi chúng được tạo. Có hai cách để thiết lập các thuộc tính:

```cmake
set_property(TARGET TargetName
             PROPERTY CXX_STANDARD 11)

set_target_properties(TargetName PROPERTIES
                      CXX_STANDARD 11)
```

Dạng đầu tiên tổng quát hơn và có thể thiết lập nhiều mục tiêu/tệp/kiểm tra cùng một lúc và có các tùy chọn hữu ích. Dạng thứ hai là một phím tắt để thiết lập một số thuộc tính trên một mục tiêu. Và bạn có thể nhận các thuộc tính tương tự:

```cmake
get_property(ResultVariable TARGET TargetName PROPERTY CXX_STANDARD)
```

Xem [cmake-properties] để biết danh sách tất cả các thuộc tính đã biết. Bạn cũng có thể tự tạo trong một số trường hợp. [^2]

[cmake-properties]: https://cmake.org/cmake/help/latest/manual/cmake-properties.7.html
[cmake-variables]: https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html

[^1]: Các câu lệnh `if` hơi kỳ lạ ở chỗ chúng có thể lấy biến có hoặc không có cú pháp bao quanh; điều này là vì lý do lịch sử: `if` có trước cú pháp `${}`.
[^2]: Ví dụ: các mục tiêu giao diện có thể có giới hạn về các thuộc tính tùy chỉnh được phép.