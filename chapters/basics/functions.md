# Lập trình trong CMake

## Luồng điều khiển

CMake có một câu lệnh {{ command.format('if') }}, mặc dù qua nhiều năm nó đã trở nên khá phức tạp. Có một loạt các từ khóa viết hoa mà bạn có thể sử dụng bên trong một câu lệnh if, và bạn thường có thể tham chiếu đến các biến bằng cách trực tiếp sử dụng tên hoặc cú pháp `${}` (câu lệnh if lịch sử có trước việc mở rộng biến). Một ví dụ về câu lệnh if:

```cmake
if(variable)
    # Nếu biến là `ON`, `YES`, `TRUE`, `Y`, hoặc số khác 0
else()
    # Nếu biến là `0`, `OFF`, `NO`, `FALSE`, `N`, `IGNORE`, `NOTFOUND`, `""`, hoặc kết thúc bằng `-NOTFOUND`
endif()
# Nếu biến không mở rộng thành một trong những giá trị trên, CMake sẽ mở rộng nó rồi thử lại
```

Vì điều này có thể hơi khó hiểu nếu bạn rõ ràng đặt một biến mở rộng, như `${variable}`, do khả năng mở rộng của một mở rộng, một chính sách ({{ policy.format('CMP0054') }}) đã được thêm vào trong CMake 3.1+ để giữ cho một mở rộng có dấu ngoặc kép không bị mở rộng lại lần nữa. Vì vậy, miễn là phiên bản tối thiểu của CMake là 3.1+, bạn có thể làm:

```cmake
if("${variable}")
    # Đúng nếu biến không giống false
else()
    # Lưu ý rằng các biến không được định nghĩa sẽ là `""` do đó là false
endif()
```
Có nhiều từ khóa khác nhau, chẳng hạn như:

- Đơn nguyên: `NOT`, `TARGET`, `EXISTS` (file), `DEFINED`, v.v.
- Nhị nguyên: `STREQUAL`, `AND`, `OR`, `MATCHES` (biểu thức chính quy), `VERSION_LESS`, `VERSION_LESS_EQUAL` (CMake 3.7+), v.v.
- Dấu ngoặc đơn có thể được sử dụng để nhóm

## {{ cmake.format('generator-expressions') }}

{{ cmake.format('generator-expressions') }} rất mạnh mẽ, nhưng hơi lạ và chuyên biệt. Hầu hết các lệnh CMake xảy ra tại thời điểm cấu hình, bao gồm cả các câu lệnh if đã thấy ở trên. Nhưng nếu bạn cần logic xảy ra tại thời điểm build hoặc thậm chí cài đặt thì sao? Biểu thức generator được thêm vào cho mục đích này.[^1] Chúng được đánh giá trong các thuộc tính mục tiêu.

Biểu thức generator đơn giản nhất là các biểu thức thông tin, và có dạng `$<KEYWORD>`; chúng đánh giá một mẩu thông tin liên quan đến cấu hình hiện tại. Dạng khác là `$<KEYWORD:value>`, trong đó `KEYWORD` là một từ khóa kiểm soát việc đánh giá, và giá trị là mục cần đánh giá (một từ khóa biểu thức thông tin cũng được phép ở đây). Nếu KEYWORD là một biểu thức generator hoặc biến đánh giá thành 0 hoặc 1, `value` sẽ được thay thế nếu là 1 và không nếu là 0. Bạn có thể lồng các biểu thức generator, và bạn có thể sử dụng biến để làm cho việc đọc các biến lồng nhau dễ chịu hơn. Một số biểu thức cho phép nhiều giá trị, được ngăn cách bằng dấu phẩy.[^2]

Nếu bạn muốn đặt một cờ biên dịch chỉ cho cấu hình DEBUG, ví dụ, bạn có thể làm:

```
target_compile_options(MyTarget PRIVATE "$<$<CONFIG:Debug>:--my-flag>")
```

Đây là một cách mới hơn, tốt hơn để thêm các thứ so với việc sử dụng các biến `*_DEBUG` chuyên biệt, và tổng quát hóa cho tất cả các thứ mà biểu thức generator hỗ trợ. Lưu ý rằng bạn không bao giờ, không bao giờ nên sử dụng giá trị thời gian cấu hình cho cấu hình hiện tại, vì các generator đa cấu hình như IDE không có "cấu hình hiện tại" tại thời điểm cấu hình, chỉ có tại thời điểm build thông qua biểu thức generator và các biến `*_<CONFIG>` tùy chỉnh.

Các cách sử dụng phổ biến khác cho biểu thức generator:

- Giới hạn một mục cho một ngôn ngữ nhất định, chẳng hạn như CXX, để tránh nó trộn lẫn với thứ gì đó như CUDA, hoặc bao bọc nó để nó khác nhau tùy thuộc vào ngôn ngữ mục tiêu.
- Truy cập các thuộc tính phụ thuộc vào cấu hình, như vị trí tệp mục tiêu.
- Cung cấp một vị trí khác cho các thư mục build và cài đặt.

Điều cuối cùng này rất phổ biến. Bạn sẽ thấy điều gì đó như thế này trong hầu hết mọi gói hỗ trợ cài đặt:

```cmake
target_include_directories(
    MyTarget
  PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
    $<INSTALL_INTERFACE:include>
)
```
## Macro và Hàm

Bạn có thể dễ dàng định nghĩa hàm {{ command.format('function') }} hoặc {{ command.format('macro') }} của riêng mình trong CMake. Sự khác biệt duy nhất giữa hàm và macro là phạm vi; macro không có phạm vi. Vì vậy, nếu bạn đặt một biến trong một hàm và muốn nó hiển thị bên ngoài, bạn sẽ cần `PARENT_SCOPE`. Do đó, việc lồng các hàm hơi phức tạp, vì bạn sẽ phải rõ ràng đặt các biến mà bạn muốn hiển thị ra bên ngoài thành `PARENT_SCOPE` trong mỗi hàm. Nhưng, các hàm không "rò rỉ" tất cả các biến của chúng như macro. Đối với các ví dụ sau, tôi sẽ sử dụng hàm.

Một ví dụ về một hàm đơn giản như sau:

```cmake
function(SIMPLE REQUIRED_ARG)
    message(STATUS "Simple arguments: ${REQUIRED_ARG}, followed by ${ARGN}")
    set(${REQUIRED_ARG} "From SIMPLE" PARENT_SCOPE)
endfunction()

simple(This Foo Bar)
message("Output: ${This}")
```

Đầu ra sẽ là:

```
-- Simple arguments: This, followed by Foo;Bar
Output: From SIMPLE
```

Nếu bạn muốn các đối số vị trí, chúng được liệt kê rõ ràng, và tất cả các đối số khác được thu thập trong `ARGN` (`ARGV` giữ tất cả các đối số, kể cả những cái bạn liệt kê). Bạn phải làm việc xung quanh thực tế rằng CMake không có giá trị trả về bằng cách đặt các biến. Trong ví dụ trên, bạn có thể rõ ràng đưa ra một tên biến để đặt.

## Đối số

CMake có một hệ thống biến có tên mà bạn đã thấy trong hầu hết các hàm dựng sẵn trong CMake. Bạn có thể sử dụng nó với hàm {{ command.format('cmake_parse_arguments') }}. Nếu bạn muốn hỗ trợ một phiên bản CMake nhỏ hơn 3.5, bạn cũng sẽ muốn bao gồm module {{ module.format('CMakeParseArguments') }}, nơi mà nó từng tồn tại trước khi trở thành một lệnh dựng sẵn. Dưới đây là một ví dụ về cách sử dụng nó:
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

Bên trong hàm sau khi gọi, bạn sẽ thấy:

```
COMPLEX_PREFIX_SINGLE = TRUE
COMPLEX_PREFIX_ANOTHER = FALSE
COMPLEX_PREFIX_ONE_VALUE = "value"
COMPLEX_PREFIX_ALSO_ONE_VALUE = <UNDEFINED>
COMPLEX_PREFIX_MULTI_VALUES = "some;other;values"
```

Nếu bạn nhìn vào trang chính thức, bạn sẽ thấy một phương pháp hơi khác sử dụng set để tránh việc phải viết rõ ràng các dấu chấm phẩy trong danh sách; hãy thoải mái sử dụng cấu trúc mà bạn thích nhất. Bạn có thể kết hợp nó với các đối số vị trí được liệt kê ở trên; bất kỳ đối số còn lại nào (do đó là các đối số vị trí tùy chọn) đều nằm trong `COMPLEX_PREFIX_UNPARSED_ARGUMENTS`.

[^1]: Chúng hoạt động như thể chúng được đánh giá tại thời điểm build/cài đặt, mặc dù thực tế chúng được đánh giá cho mỗi cấu hình build.
[^2]: Tài liệu CMake chia các biểu thức thành Thông tin, Logic và Đầu ra.