# Lập trình trong CMake

## Điều khiển luồng

CMake có câu lệnh {{ command.format('if') }}, mặc dù qua nhiều năm nó đã trở nên khá phức tạp. Có một loạt các từ khóa viết hoa mà bạn có thể sử dụng bên trong câu lệnh if, và bạn thường có thể tham chiếu đến các biến bằng cách trực tiếp bằng tên hoặc sử dụng cú pháp `${}` (câu lệnh if trong lịch sử có trước khi mở rộng biến). Một ví dụ về câu lệnh if:

```cmake
if(variable)
    # Nếu biến là `ON`, `YES`, `TRUE`, `Y` hoặc số khác không
else()
    # Nếu biến là `0`, `OFF`, `NO`, `FALSE`, `N`, `IGNORE`, `NOTFOUND`, `""` hoặc kết thúc bằng `-NOTFOUND`
endif()
# Nếu biến không được mở rộng thành một trong các biến trên, CMake sẽ mở rộng nó rồi thử lại
```

Vì điều này có thể hơi khó hiểu nếu bạn đặt rõ ràng một phần mở rộng biến, chẳng hạn như `${variable}`, do khả năng mở rộng của một phần mở rộng, một chính sách ({{ policy.format('CMP0054') }}) đã được thêm vào CMake 3.1+ để giữ cho một phần mở rộng được trích dẫn không bị mở rộng một lần nữa. Vì vậy, miễn là phiên bản tối thiểu của CMake là 3.1+, bạn có thể làm:

```cmake
if("${variable}")
    # Đúng nếu biến không giống false
else()
    # Lưu ý rằng các biến không xác định sẽ là `""` do đó là false
endif()
```

Ngoài ra còn có nhiều từ khóa, chẳng hạn như:

- Đơn nhất: `NOT`, `TARGET`, `EXISTS` (tệp), `DEFINED`, v.v.
- Nhị phân: `STREQUAL`, `AND`, `OR`, `MATCHES` (biểu thức chính quy), `VERSION_LESS`, `VERSION_LESS_EQUAL` (CMake 3.7+), v.v.
- Dấu ngoặc đơn có thể được sử dụng để nhóm

## {{ cmake.format('generator-expressions') }}

{{ cmake.format('generator-expressions') }} thực sự mạnh mẽ, nhưng hơi kỳ lạ và chuyên biệt. Hầu hết các lệnh CMake xảy ra tại thời điểm cấu hình, bao gồm các câu lệnh if đã thấy ở trên. Nhưng điều gì sẽ xảy ra nếu bạn cần logic xảy ra tại thời điểm build hoặc thậm chí thời điểm cài đặt? Các biểu thức trình tạo đã được thêm vào cho mục đích này. [^1] Chúng được đánh giá trong các thuộc tính mục tiêu.

Các biểu thức trình tạo đơn giản nhất là các biểu thức thông tin và có dạng `$<KEYWORD>`; chúng đánh giá một phần thông tin có liên quan đến cấu hình hiện tại. Dạng khác là `$<KEYWORD:value>`, trong đó `KEYWORD` là một từ khóa điều khiển việc đánh giá và value là mục cần đánh giá (một từ khóa biểu thức thông tin cũng được phép ở đây). Nếu KEYWORD là một biểu thức trình tạo hoặc biến được đánh giá là 0 hoặc 1, `value` sẽ được thay thế nếu là 1 và không thay thế nếu là 0. Bạn có thể lồng các biểu thức trình tạo và bạn có thể sử dụng các biến để giúp việc đọc các biến lồng nhau trở nên dễ dàng hơn. Một số biểu thức cho phép nhiều giá trị, được phân tách bằng dấu phẩy. [^2]

Ví dụ: nếu bạn muốn đặt cờ biên dịch chỉ cho cấu hình DEBUG, bạn có thể làm như sau:

```
target_compile_options(MyTarget PRIVATE "$<$<CONFIG:Debug>:--my-flag>")
```

Đây là một cách mới hơn, tốt hơn để thêm các thứ hơn là sử dụng các biến `*_DEBUG` chuyên biệt và được khái quát hóa cho tất cả những thứ mà các biểu thức trình tạo hỗ trợ. Lưu ý rằng bạn không bao giờ nên sử dụng giá trị thời gian cấu hình cho cấu hình hiện tại, bởi vì các trình tạo đa cấu hình như IDE không có cấu hình "hiện tại" tại thời điểm cấu hình, mà chỉ có tại thời điểm build thông qua các biểu thức trình tạo và các biến `*_<CONFIG>` tùy chỉnh.

Các cách sử dụng phổ biến khác cho các biểu thức trình tạo:

- Giới hạn một mục chỉ cho một ngôn ngữ nhất định, chẳng hạn như CXX, để tránh nó trộn lẫn với một thứ gì đó như CUDA, hoặc bao bọc nó để nó khác nhau tùy thuộc vào ngôn ngữ mục tiêu.
- Truy cập các thuộc tính phụ thuộc vào cấu hình, chẳng hạn như vị trí tệp mục tiêu.
- Cung cấp một vị trí khác nhau cho các thư mục build và cài đặt.

Cách cuối cùng đó rất phổ biến. Bạn sẽ thấy một cái gì đó như thế này trong hầu hết mọi gói hỗ trợ cài đặt:

```cmake
target_include_directories(
    MyTarget
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)
```

## Macro và Hàm

Bạn có thể dễ dàng định nghĩa {{ command.format('function') }} hoặc {{ command.format('macro') }} CMake của riêng mình. Sự khác biệt duy nhất giữa hàm và macro là phạm vi; macro không có. Vì vậy, nếu bạn đặt một biến trong hàm và muốn nó hiển thị bên ngoài, bạn sẽ cần `PARENT_SCOPE`. Do đó, việc lồng các hàm hơi phức tạp, vì bạn sẽ phải đặt rõ ràng các biến mà bạn muốn hiển thị với thế giới bên ngoài thành `PARENT_SCOPE` trong mỗi hàm. Tuy nhiên, các hàm không "rò rỉ" tất cả các biến của chúng như macro. Đối với các ví dụ sau, tôi sẽ sử dụng các hàm.

Một ví dụ về hàm đơn giản như sau:

```cmake
function(SIMPLE REQUIRED_ARG)
    message(STATUS "Các đối số đơn giản: ${REQUIRED_ARG}, theo sau là ${ARGN}")
    set(${REQUIRED_ARG} "Từ SIMPLE" PARENT_SCOPE)
endfunction()

simple(This Foo Bar)
message("Đầu ra: ${This}")
```

Đầu ra sẽ là:

```
-- Các đối số đơn giản: This, theo sau là Foo;Bar
Đầu ra: Từ SIMPLE
```

Nếu bạn muốn các đối số vị trí, chúng được liệt kê rõ ràng và tất cả các đối số khác được thu thập trong `ARGN` (`ARGV` chứa tất cả các đối số, ngay cả những đối số bạn liệt kê). Bạn phải giải quyết thực tế là CMake không có giá trị trả về bằng cách đặt các biến. Trong ví dụ trên, bạn có thể đặt rõ ràng tên biến để đặt.

## Đối số

CMake có một hệ thống biến được đặt tên mà bạn đã thấy trong hầu hết các hàm dựng sẵn của CMake. Bạn có thể sử dụng nó với hàm {{ command.format('cmake_parse_arguments') }}. Nếu bạn muốn hỗ trợ phiên bản CMake nhỏ hơn 3.5, bạn cũng sẽ muốn include mô-đun {{ module.format('CMakeParseArguments') }}, nơi nó từng tồn tại trước khi trở thành lệnh tích hợp. Dưới đây là ví dụ về cách sử dụng nó:

```cmake
function(COMPLEX)
    cmake_parse_arguments(
        COMPLEX_PREFIX
        "SINGLE;ANOTHER"
        "ONE_VALUE;ALSO_ONE_VALUE"
        "MULTI_VALUES"
        ${ARGN}
    )
endfunction()

complex(SINGLE ONE_VALUE value MULTI_VALUES some other values)
```

Bên trong hàm sau lệnh gọi này, bạn sẽ tìm thấy:

```
COMPLEX_PREFIX_SINGLE = TRUE
COMPLEX_PREFIX_ANOTHER = FALSE
COMPLEX_PREFIX_ONE_VALUE = "value"
COMPLEX_PREFIX_ALSO_ONE_VALUE = <UNDEFINED>
COMPLEX_PREFIX_MULTI_VALUES = "some;other;values"
```

Nếu bạn xem trang chính thức, bạn sẽ thấy một phương pháp hơi khác bằng cách sử dụng set để tránh viết rõ ràng dấu chấm phẩy trong danh sách; hãy thoải mái sử dụng cấu trúc mà bạn thích nhất. Bạn có thể kết hợp nó với các đối số vị trí được liệt kê ở trên; bất kỳ đối số còn lại nào (do đó là các đối số vị trí tùy chọn) đều nằm trong `COMPLEX_PREFIX_UNPARSED_ARGUMENTS`.

[^1]: Chúng hoạt động như thể chúng được đánh giá tại thời điểm build/cài đặt, mặc dù thực tế chúng được đánh giá cho mỗi cấu hình build.
[^2]: Tài liệu CMake chia các biểu thức thành Thông tin, Logic và Đầu ra.

