## Giới thiệu về kiến thức cơ bản

### Phiên bản tối thiểu

Dưới đây là dòng đầu tiên của mọi tệp `CMakeLists.txt`, đây là tên bắt buộc của tệp mà CMake tìm kiếm:

```cmake
cmake_minimum_required(VERSION 3.15)
```

Chúng ta hãy đề cập một chút về cú pháp CMake. Tên lệnh 
{{ command.format('cmake_minimum_required') }} không phân biệt chữ hoa chữ thường, vì vậy quy ước chung
là sử dụng chữ thường. [^1] `VERSION` là một từ khóa đặc biệt cho hàm này. 
Giá trị của phiên bản sẽ theo sau từ khóa. Giống như mọi nơi trong
cuốn sách này, chỉ cần nhấp vào tên lệnh để xem tài liệu chính thức
và sử dụng menu thả xuống để chuyển đổi tài liệu giữa các phiên bản CMake.

Dòng này đặc biệt! [^2] Phiên bản của CMake cũng sẽ quyết định các chính sách,
mà xác định các thay đổi về hành vi. Vì vậy, nếu bạn đặt `minimum_required` thành `VERSION 2.8`, bạn sẽ gặp phải hành vi liên kết sai trên macOS chẳng hạn, ngay cả trong 
các phiên bản CMake mới nhất. Nếu bạn đặt nó thành 3.3 hoặc thấp hơn, bạn sẽ gặp phải 
hành vi biểu tượng ẩn sai, v.v. Danh sách các chính sách và phiên bản có sẵn tại
{{ cmake.format('policies') }}.

Bắt đầu từ CMake 3.12, điều này hỗ trợ một phạm vi, chẳng hạn như `VERSION 3.15...3.30`;
điều này có nghĩa là bạn hỗ trợ phiên bản thấp nhất là 3.15 nhưng cũng đã thử nghiệm nó với các cài đặt chính sách mới
lên đến 3.30. Điều này tốt hơn nhiều cho những người dùng cần các cài đặt tốt hơn,
và nhờ một mẹo trong cú pháp, nó tương thích ngược với các phiên bản CMake cũ hơn
(mặc dù thực tế chạy CMake 3.1-3.11 sẽ chỉ đặt phiên bản cũ
của các chính sách, vì các phiên bản đó không xử lý điều này đặc biệt). Phiên bản mới
của các chính sách có xu hướng quan trọng nhất đối với người dùng macOS và Windows, những người
thường cũng có phiên bản CMake rất gần đây.

Đây là những gì các dự án mới nên làm:

```cmake
cmake_minimum_required(VERSION 3.15...3.30)
```


:::{tip}

Nếu bạn thực sự cần đặt thành một giá trị thấp ở đây, bạn có thể sử dụng
{{ command.format('cmake_policy') }} để tăng cấp chính sách có điều kiện hoặc đặt một
chính sách cụ thể.

:::

## Thiết lập một dự án

Bây giờ, mọi tệp CMake cấp cao nhất sẽ có dòng tiếp theo:

```cmake
project(MyProject VERSION 1.0
                  DESCRIPTION "Dự án rất hay"
                  LANGUAGES CXX)
```

Bây giờ chúng ta thấy nhiều cú pháp hơn. Chuỗi được đặt trong dấu ngoặc kép, khoảng trắng không quan trọng, và
tên của dự án là đối số đầu tiên (theo vị trí). Tất cả các từ khóa
đối số ở đây là tùy chọn. Phiên bản sẽ thiết lập một loạt các biến, như
`MyProject_VERSION` và `PROJECT_VERSION`. Các ngôn ngữ là `C`, `CXX`,
`Fortran`, `ASM`, `CUDA` (CMake 3.8+), `CSharp` (3.8+) và `SWIFT` (CMake
3.15+ thử nghiệm). `C CXX` là mặc định. Trong CMake 3.9, `DESCRIPTION` đã được
thêm vào để thiết lập mô tả dự án. Tài liệu cho
{{ command.format('project') }} có thể hữu ích.

:::{tip}

Bạn có thể thêm
[chú thích](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#comments)
với ký tự `#`. CMake cũng có cú pháp nội tuyến cho các chú thích, nhưng
nó hiếm khi được sử dụng.

:::

Thực sự không có gì đặc biệt về tên dự án. Không có mục tiêu nào được thêm vào tại thời điểm này.

## Tạo một tệp thực thi

Mặc dù thư viện thú vị hơn nhiều và chúng ta sẽ dành phần lớn thời gian
với chúng, hãy bắt đầu với một tệp thực thi đơn giản.

```cmake
add_executable(one two.cpp three.h)
```

Có một số điều cần phân tích ở đây. `one` vừa là tên của tệp thực thi được tạo, vừa là tên của mục tiêu CMake được tạo (bạn sẽ sớm nghe nhiều hơn về các mục tiêu, tôi hứa). Danh sách tệp nguồn xuất hiện tiếp theo và bạn có thể liệt kê bao nhiêu tùy thích. CMake thông minh và sẽ chỉ biên dịch các phần mở rộng tệp nguồn. Các tệp tiêu đề, với hầu hết mục đích và ý nghĩa, sẽ bị bỏ qua; lý do duy nhất để liệt kê chúng là để chúng hiển thị trong IDE. Các mục tiêu hiển thị dưới dạng thư mục trong nhiều IDE. Thông tin thêm về hệ thống bản dựng chung và mục tiêu có sẵn tại {{ cmake.format('buildsystem') }}.

## Tạo một thư viện

Tạo một thư viện được thực hiện với {{ command.format('add_library') }}, và cũng đơn giản như vậy:

```cmake
add_library(one STATIC two.cpp three.h)
```

Bạn có thể chọn một loại thư viện, STATIC, SHARED hoặc MODULE. Nếu bạn bỏ qua lựa chọn này, giá trị của `BUILD_SHARED_LIBS` sẽ được sử dụng để chọn giữa STATIC và SHARED.

Như bạn sẽ thấy trong các phần sau, thường bạn sẽ cần tạo một mục tiêu giả, nghĩa là, một mục tiêu mà không cần phải biên dịch, ví dụ, cho một thư viện chỉ có tiêu đề. Đó được gọi là thư viện INTERFACE và là một lựa chọn khác; điểm khác biệt duy nhất là nó không thể được theo sau bởi tên tệp.

Bạn cũng có thể tạo thư viện `ALIAS` với một thư viện hiện có, điều này đơn giản là cung cấp cho bạn một tên mới cho một mục tiêu. Lợi ích duy nhất của điều này là bạn có thể tạo thư viện có `::` trong tên (mà bạn sẽ thấy sau). [^3]

## Mục tiêu là bạn của bạn

Bây giờ chúng ta đã chỉ định một mục tiêu, làm cách nào để chúng ta thêm thông tin về nó? Ví dụ: có thể nó cần một thư mục include:

```cmake
target_include_directories(one PUBLIC include)
```

{{ command.format('target_include_directories') }} thêm một thư mục include vào một mục tiêu. `PUBLIC` không có ý nghĩa nhiều đối với một tệp thực thi; đối với một thư viện, nó cho CMake biết rằng bất kỳ mục tiêu nào liên kết đến mục tiêu này cũng phải cần thư mục include đó. Các tùy chọn khác là `PRIVATE` (chỉ ảnh hưởng đến mục tiêu hiện tại, không phải các phần phụ thuộc) và `INTERFACE` (chỉ cần thiết cho các phần phụ thuộc).

Sau đó, chúng ta có thể liên kết các mục tiêu:

```cmake
add_library(another STATIC another.cpp another.h)
target_link_libraries(another PUBLIC one)
```

{{ command.format('target_link_libraries') }} có lẽ là lệnh hữu ích và khó hiểu nhất trong CMake. Nó nhận một mục tiêu (`another`) và thêm một phần phụ thuộc nếu một mục tiêu được cung cấp. Nếu không tồn tại mục tiêu nào có tên đó (`one`), thì nó sẽ thêm một liên kết đến một thư viện có tên `one` trên đường dẫn của bạn (do đó có tên của lệnh). Hoặc bạn có thể cung cấp cho nó đường dẫn đầy đủ đến một thư viện. Hoặc một cờ trình liên kết. Chỉ để thêm một chút nhầm lẫn cuối cùng, CMake cổ điển cho phép bạn bỏ qua lựa chọn từ khóa `PUBLIC`, v.v. Nếu điều này được thực hiện trên một mục tiêu, bạn sẽ gặp lỗi nếu bạn cố gắng trộn các kiểu xa hơn trong chuỗi.

Hãy tập trung vào việc sử dụng mục tiêu ở mọi nơi và từ khóa ở mọi nơi, và bạn sẽ ổn thôi.

Các mục tiêu có thể có thư mục include, thư viện được liên kết (hoặc mục tiêu được liên kết), tùy chọn biên dịch, định nghĩa biên dịch, tính năng biên dịch (xem chương C++11) và hơn thế nữa. Như bạn sẽ thấy trong hai chương bao gồm các dự án, bạn thường có thể nhận được các mục tiêu (và luôn tạo các mục tiêu) để đại diện cho tất cả các thư viện bạn sử dụng. Ngay cả những thứ không phải là thư viện thực sự, như OpenMP, cũng có thể được biểu diễn bằng các mục tiêu. Đây là lý do tại sao Modern CMake rất tuyệt vời!

## Đi sâu vào

Hãy xem liệu bạn có thể theo dõi tệp sau đây không. Nó tạo ra một thư viện C++11 đơn giản và một chương trình sử dụng nó. Không có phần phụ thuộc. Tôi sẽ thảo luận thêm về các tùy chọn tiêu chuẩn C++ sau, sử dụng hệ thống CMake 3.8 ngay bây giờ.

```cmake
cmake_minimum_required(VERSION 3.15...3.30)

project(Calculator LANGUAGES CXX)

add_library(calclib STATIC src/calclib.cpp include/calc/lib.hpp)
target_include_directories(calclib PUBLIC include)
target_compile_features(calclib PUBLIC cxx_std_11)

add_executable(calc apps/calc.cpp)
target_link_libraries(calc PUBLIC calclib)

```

[^1]: Trong cuốn sách này, tôi sẽ chủ yếu tránh hiển thị cho bạn cách sai để làm mọi việc; bạn có thể tìm thấy rất nhiều ví dụ về điều đó trực tuyến. Tôi sẽ đề cập đến các lựa chọn thay thế đôi khi, nhưng những điều này không được khuyến khích trừ khi chúng là hoàn toàn cần thiết; thường thì chúng chỉ ở đó để giúp bạn đọc mã CMake cũ hơn.
[^2]: Đôi khi bạn sẽ thấy `FATAL_ERROR` ở đây, điều đó là cần thiết để hỗ trợ các lỗi rõ ràng khi chạy CMake <2.6, điều này sẽ không còn là vấn đề nữa.
[^3]: Cú pháp `::` ban đầu được dành cho các thư viện `INTERFACE IMPORTED`, được cho là các thư viện được định nghĩa bên ngoài dự án hiện tại. Nhưng, vì điều này, hầu hết các lệnh `target_*` không hoạt động trên các thư viện `IMPORTED`, khiến chúng khó tự thiết lập. Vì vậy, đừng sử dụng từ khóa `IMPORTED` ngay bây giờ và sử dụng mục tiêu `ALIAS` thay thế; nó sẽ ổn cho đến khi bạn bắt đầu xuất khẩu mục tiêu. Hạn chế này đã được khắc phục trong CMake 3.11.