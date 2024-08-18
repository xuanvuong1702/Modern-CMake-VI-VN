# Biến và Bộ nhớ đệm

## Biến cục bộ

Chúng ta sẽ bắt đầu với các biến. Một biến cục bộ được thiết lập như sau:

```CMake
set(MY_VARIABLE "value")
```

Tên của các biến thường được viết hoa toàn bộ, và giá trị theo sau. Bạn truy cập một biến bằng cách sử dụng `${}`, chẳng hạn `${MY_VARIABLE}`.[^1] CMake có khái niệm về phạm vi; bạn có thể truy cập giá trị của biến sau khi bạn thiết lập nó miễn là bạn đang ở trong cùng một phạm vi. Nếu bạn rời khỏi một hàm hoặc một tệp trong thư mục con, biến sẽ không còn được định nghĩa. Bạn có thể thiết lập một biến trong phạm vi ngay trên phạm vi hiện tại của bạn bằng cách sử dụng `PARENT_SCOPE` ở cuối.

Danh sách đơn giản là một chuỗi các giá trị khi bạn thiết lập chúng:

```cmake
set(MY_LIST "one" "two")
```

mà nội bộ sẽ trở thành các giá trị được phân tách bằng dấu `;`. Vì vậy, đây là một câu lệnh tương tự:

```cmake
set(MY_LIST "one;two")
```
Lệnh `list(` có các tiện ích để làm việc với danh sách, và `separate_arguments` sẽ biến một chuỗi phân tách bằng dấu cách thành một danh sách (tại chỗ). Lưu ý rằng một giá trị không có dấu ngoặc kép trong CMake cũng giống như một giá trị có dấu ngoặc kép nếu không có dấu cách trong đó; điều này cho phép bạn bỏ qua dấu ngoặc kép hầu hết thời gian khi làm việc với giá trị mà bạn biết không thể chứa dấu cách.

Khi một biến được mở rộng bằng cú pháp `${}`, tất cả các quy tắc về dấu cách đều áp dụng. Hãy đặc biệt cẩn thận với các đường dẫn; đường dẫn có thể chứa dấu cách bất cứ lúc nào và luôn nên được đặt trong dấu ngoặc kép khi chúng là một biến (không bao giờ viết `${MY_PATH}`, luôn nên là `"${MY_PATH}"`).

## Biến Cache

Nếu bạn muốn thiết lập một biến từ dòng lệnh, CMake cung cấp một biến cache. Một số biến đã có sẵn ở đây, như `CMAKE_BUILD_TYPE`. Cú pháp để khai báo một biến và thiết lập nó nếu nó chưa được thiết lập là:

```cmake
set(MY_CACHE_VARIABLE "VALUE" CACHE STRING "Description")
```

Điều này sẽ **không** thay thế một giá trị hiện có. Điều này là để bạn có thể thiết lập chúng trên dòng lệnh và không bị ghi đè khi tệp CMake thực thi. Nếu bạn muốn sử dụng các biến này như một biến toàn cục tạm thời, thì bạn có thể làm:

```cmake
set(MY_CACHE_VARIABLE "VALUE" CACHE STRING "" FORCE)
mark_as_advanced(MY_CACHE_VARIABLE)
```

Dòng đầu tiên sẽ khiến giá trị được thiết lập bất kể điều gì, và dòng thứ hai sẽ giữ biến không xuất hiện trong danh sách các biến nếu bạn chạy `cmake -L ..` hoặc sử dụng GUI. Điều này rất phổ biến, bạn cũng có thể sử dụng kiểu `INTERNAL` để làm điều tương tự (mặc dù về mặt kỹ thuật nó buộc kiểu STRING, điều này sẽ không ảnh hưởng đến bất kỳ mã CMake nào phụ thuộc vào biến):

```cmake
set(MY_CACHE_VARIABLE "VALUE" CACHE INTERNAL "")
```
Vì `BOOL` là một kiểu biến rất phổ biến, bạn có thể thiết lập nó ngắn gọn hơn với lệnh tắt:

```cmake
option(MY_OPTION "Có thể thiết lập từ dòng lệnh" OFF)
```

Đối với kiểu dữ liệu `BOOL`, có nhiều cách viết khác nhau cho `ON` và `OFF`.

Xem [cmake-variables] để biết danh sách các biến đã biết trong CMake.

## Biến môi trường

Bạn cũng có thể `set(ENV{variable_name} value)` và lấy biến môi trường `$ENV{variable_name}`, mặc dù nói chung nên tránh sử dụng chúng.

## Bộ nhớ đệm

Bộ nhớ đệm thực chất chỉ là một tệp văn bản, `CMakeCache.txt`, được tạo ra trong thư mục build khi bạn chạy CMake. Đây là cách CMake ghi nhớ bất kỳ thứ gì bạn đã thiết lập, vì vậy bạn không phải liệt kê lại các tùy chọn của mình mỗi khi bạn chạy lại CMake.

## Thuộc tính

Cách khác mà CMake lưu trữ thông tin là trong các thuộc tính. Đây giống như một biến, nhưng nó được gắn vào một mục khác, như một thư mục hoặc một mục tiêu. Một thuộc tính toàn cục có thể là một biến toàn cục không được lưu trong bộ nhớ đệm. Nhiều thuộc tính mục tiêu được khởi tạo từ một biến tương ứng với `CMAKE_` ở đầu. Vì vậy, thiết lập `CMAKE_CXX_STANDARD`, ví dụ, sẽ có nghĩa là tất cả các mục tiêu mới được tạo sẽ có `CXX_STANDARD` được thiết lập khi chúng được tạo. Có hai cách để thiết lập thuộc tính:

```cmake
set_property(TARGET TargetName
             PROPERTY CXX_STANDARD 11)

set_target_properties(TargetName PROPERTIES
                      CXX_STANDARD 11)
```

Hình thức đầu tiên tổng quát hơn, và có thể thiết lập nhiều mục tiêu/tệp/kiểm tra cùng một lúc, và có các tùy chọn hữu ích. Hình thức thứ hai là một lối tắt để thiết lập nhiều thuộc tính trên một mục tiêu. Và bạn có thể lấy thuộc tính tương tự như sau:

```cmake
get_property(ResultVariable TARGET TargetName PROPERTY CXX_STANDARD)
```

Xem [cmake-properties] để biết danh sách tất cả các thuộc tính đã biết. Bạn cũng có thể tạo thuộc tính của riêng mình trong một số trường hợp.[^2]

[cmake-properties]: https://cmake.org/cmake/help/latest/manual/cmake-properties.7.html
[cmake-variables]: https://cmake.org/cmake/help/latest/manual/cmake-variables.7.html

[^1]: Các câu lệnh `if` hơi kỳ lạ ở chỗ chúng có thể lấy biến có hoặc không có cú pháp bao quanh; điều này tồn tại vì lý do lịch sử: `if` có trước cú pháp `${}`.
[^2]: Các mục tiêu giao diện, chẳng hạn, có thể có giới hạn về các thuộc tính tùy chỉnh được phép.