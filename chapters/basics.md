# Giới thiệu về những điều cơ bản

## Phiên bản tối thiểu

Đây là dòng đầu tiên của mọi `CMakeLists.txt`, là tên tệp bắt buộc mà CMake tìm kiếm:


```cmake
cmake_minimum_required(VERSION 3.15)
```
Hãy đề cập một chút về cú pháp của CMake. Tên lệnh
{{ command.format('cmake_minimum_required') }} không phân biệt chữ hoa chữ thường, vì vậy thực hành phổ biến
là sử dụng chữ thường. [^1] `VERSION` là một từ khóa đặc biệt cho hàm này.
Và giá trị của phiên bản theo sau từ khóa. Như ở mọi nơi trong
cuốn sách này, chỉ cần nhấp vào tên lệnh để xem tài liệu chính thức,
và sử dụng menu thả xuống để chuyển đổi tài liệu giữa các phiên bản CMake.

Dòng này đặc biệt! [^2] Phiên bản của CMake cũng sẽ quyết định các chính sách,
định nghĩa các thay đổi hành vi. Vì vậy, nếu bạn đặt `minimum_required` thành `VERSION 2.8`, bạn sẽ gặp phải hành vi liên kết sai trên macOS, chẳng hạn, ngay cả trong các
phiên bản CMake mới nhất. Nếu bạn đặt nó thành 3.3 hoặc thấp hơn, bạn sẽ gặp phải hành vi
ẩn ký hiệu sai, v.v. Một danh sách các chính sách và phiên bản có sẵn tại
{{ cmake.format('policies') }}.

Bắt đầu từ CMake 3.12, điều này hỗ trợ một phạm vi, chẳng hạn như `VERSION 3.15...3.30`;
điều này có nghĩa là bạn hỗ trợ ít nhất là 3.15 nhưng cũng đã kiểm tra nó với các thiết lập chính sách mới
đến 3.30. Điều này thân thiện hơn nhiều với người dùng cần các thiết lập tốt hơn, và nhờ một mẹo trong cú pháp, nó tương thích ngược với các phiên bản cũ hơn của CMake (mặc dù thực tế chạy CMake 3.1-3.11 sẽ chỉ đặt phiên bản cũ của các chính sách, vì các phiên bản đó không xử lý điều này đặc biệt). Các phiên bản mới của các chính sách thường quan trọng nhất đối với người dùng macOS và Windows, những người cũng thường có phiên bản CMake rất mới.

Đây là những gì các dự án mới nên làm:

```cmake
cmake_minimum_required(VERSION 3.15...3.30)
```


:::{tip}

Nếu bạn thực sự cần đặt giá trị thấp ở đây, bạn có thể sử dụng
{{ command.format('cmake_policy') }} để tăng mức chính sách một cách có điều kiện hoặc đặt một
chính sách cụ thể.

:::

## Thiết lập một dự án

Bây giờ, mọi tệp CMake cấp cao nhất sẽ có dòng sau:

```cmake
project(MyProject VERSION 1.0
                  DESCRIPTION "Very nice project"
                  LANGUAGES CXX)
```
Bây giờ chúng ta thấy thêm cú pháp. Các chuỗi được đặt trong dấu ngoặc kép, khoảng trắng không quan trọng, và tên của dự án là đối số đầu tiên (theo vị trí). Tất cả các đối số từ khóa ở đây đều là tùy chọn. Phiên bản thiết lập một loạt các biến, như `MyProject_VERSION` và `PROJECT_VERSION`. Các ngôn ngữ là `C`, `CXX`, `Fortran`, `ASM`, `CUDA` (CMake 3.8+), `CSharp` (3.8+), và `SWIFT` (CMake 3.15+ thử nghiệm). `C CXX` là mặc định. Trong CMake 3.9, `DESCRIPTION` đã được thêm vào để thiết lập mô tả dự án. Tài liệu cho {{ command.format('project') }} có thể hữu ích.

:::{tip}

Bạn có thể thêm
[bình luận](https://cmake.org/cmake/help/latest/manual/cmake-language.7.html#comments)
với ký tự `#`. CMake cũng có cú pháp bình luận nội tuyến, nhưng hiếm khi được sử dụng.

:::
Không có gì đặc biệt về tên dự án. Không có mục tiêu nào được thêm vào tại thời điểm này.

## Tạo một tệp thực thi

Mặc dù thư viện thú vị hơn nhiều, và chúng ta sẽ dành phần lớn thời gian với chúng, hãy bắt đầu với một tệp thực thi đơn giản.

```cmake
add_executable(one two.cpp three.h)
```
Có một số điều cần giải thích ở đây. `one` vừa là tên của tệp thực thi được tạo ra, vừa là tên của mục tiêu CMake được tạo ra (bạn sẽ nghe nhiều hơn về các mục tiêu sớm thôi, tôi hứa). Danh sách tệp nguồn đi kèm ngay sau đó, và bạn có thể liệt kê bao nhiêu tùy thích. CMake rất thông minh, và sẽ chỉ biên dịch các tệp có phần mở rộng nguồn. Các tệp tiêu đề sẽ, cho hầu hết các mục đích, bị bỏ qua; lý do duy nhất để liệt kê chúng là để chúng hiển thị trong các IDE. Các mục tiêu xuất hiện dưới dạng thư mục trong nhiều IDE. Thông tin thêm về hệ thống xây dựng chung và các mục tiêu có sẵn tại {{ cmake.format('buildsystem') }}.

## Tạo một thư viện

Tạo một thư viện được thực hiện với {{ command.format('add_library') }}, và cũng đơn giản như vậy:

```cmake
add_library(one STATIC two.cpp three.h)
```

Bạn có thể chọn loại thư viện, STATIC, SHARED, hoặc MODULE. Nếu bạn bỏ qua lựa chọn này, giá trị của `BUILD_SHARED_LIBS` sẽ được sử dụng để chọn giữa STATIC và SHARED.

Như bạn sẽ thấy trong các phần tiếp theo, thường bạn sẽ cần tạo một mục tiêu giả, tức là một mục tiêu không cần biên dịch gì cả, ví dụ như cho một thư viện chỉ có tiêu đề. Đó được gọi là thư viện INTERFACE, và là một lựa chọn khác; điểm khác biệt duy nhất là nó không thể đi kèm với tên tệp.

Bạn cũng có thể tạo một thư viện `ALIAS` với một thư viện hiện có, đơn giản chỉ là cung cấp cho bạn một tên mới cho một mục tiêu. Lợi ích duy nhất của việc này là bạn có thể tạo các thư viện với `::` trong tên (như bạn sẽ thấy sau). [^3]

## Mục tiêu là bạn của bạn

Bây giờ chúng ta đã chỉ định một mục tiêu, làm thế nào để thêm thông tin về nó? Ví dụ, có thể nó cần một thư mục include:

```cmake
target_include_directories(one PUBLIC include)
```
{{ command.format('target_include_directories') }} thêm một thư mục include vào mục tiêu. `PUBLIC` không có nhiều ý nghĩa đối với một tệp thực thi; đối với một thư viện, nó cho CMake biết rằng bất kỳ mục tiêu nào liên kết với mục tiêu này cũng cần thư mục include đó. Các tùy chọn khác là `PRIVATE` (chỉ ảnh hưởng đến mục tiêu hiện tại, không ảnh hưởng đến các phụ thuộc), và `INTERFACE` (chỉ cần cho các phụ thuộc).

Chúng ta có thể liên kết các mục tiêu với nhau:


```cmake
add_library(another STATIC another.cpp another.h)
target_link_libraries(another PUBLIC one)
```
{{ command.format('target_link_libraries') }} có lẽ là lệnh hữu ích và gây nhầm lẫn nhất trong CMake. Nó nhận một mục tiêu (`another`) và thêm một phụ thuộc nếu một mục tiêu được cung cấp. Nếu không có mục tiêu nào tên (`one`) tồn tại, thì nó sẽ thêm một liên kết đến một thư viện có tên `one` trên đường dẫn của bạn (do đó có tên của lệnh này). Hoặc bạn có thể cung cấp cho nó một đường dẫn đầy đủ đến một thư viện. Hoặc một cờ liên kết. Để thêm một chút nhầm lẫn cuối cùng, CMake cổ điển cho phép bạn bỏ qua việc chọn từ khóa `PUBLIC`, v.v. Nếu điều này được thực hiện trên một mục tiêu, bạn sẽ gặp lỗi nếu cố gắng trộn các kiểu sau này trong chuỗi.

Hãy tập trung vào việc sử dụng mục tiêu ở mọi nơi, và từ khóa ở mọi nơi, và bạn sẽ ổn thôi.

Các mục tiêu có thể có thư mục include, thư viện liên kết (hoặc mục tiêu liên kết), tùy chọn biên dịch, định nghĩa biên dịch, tính năng biên dịch (xem chương C++11), và nhiều hơn nữa. Như bạn sẽ thấy trong hai chương về dự án bao gồm, bạn có thể thường xuyên nhận được các mục tiêu (và luôn tạo mục tiêu) để đại diện cho tất cả các thư viện bạn sử dụng. Ngay cả những thứ không phải là thư viện thực sự, như OpenMP, cũng có thể được đại diện bằng các mục tiêu. Đây là lý do tại sao CMake hiện đại rất tuyệt vời!
## Bắt đầu

Hãy thử xem bạn có thể theo dõi tệp sau đây không. Nó tạo ra một thư viện C++11 đơn giản và một chương trình sử dụng thư viện đó. Không có phụ thuộc nào. Tôi sẽ thảo luận thêm về các tùy chọn tiêu chuẩn C++ sau, hiện tại chúng ta sẽ sử dụng hệ thống CMake 3.8.

```cmake
cmake_minimum_required(VERSION 3.15...3.30)

project(Calculator LANGUAGES CXX)

add_library(calclib STATIC src/calclib.cpp include/calc/lib.hpp)
target_include_directories(calclib PUBLIC include)
target_compile_features(calclib PUBLIC cxx_std_11)

add_executable(calc apps/calc.cpp)
target_link_libraries(calc PUBLIC calclib)

```
[^1]: Trong cuốn sách này, tôi sẽ tránh việc chỉ cho bạn cách làm sai; bạn có thể tìm thấy rất nhiều ví dụ về điều đó trên mạng. Thỉnh thoảng tôi sẽ đề cập đến các phương án thay thế, nhưng chúng không được khuyến nghị trừ khi thực sự cần thiết; thường thì chúng chỉ giúp bạn đọc mã CMake cũ hơn.
[^2]: Đôi khi bạn sẽ thấy `FATAL_ERROR` ở đây, điều này cần thiết để hỗ trợ các lỗi đẹp khi chạy trong CMake <2.6, điều này không còn là vấn đề nữa.
[^3]: Cú pháp `::` ban đầu được dự định cho các thư viện `INTERFACE IMPORTED`, vốn được cho là các thư viện được định nghĩa bên ngoài dự án hiện tại. Nhưng, vì lý do này, hầu hết các lệnh `target_*` không hoạt động trên các thư viện `IMPORTED`, khiến chúng khó thiết lập. Vì vậy, đừng sử dụng từ khóa `IMPORTED` vào lúc này, hãy sử dụng mục tiêu `ALIAS` thay thế; nó sẽ ổn cho đến khi bạn bắt đầu xuất các mục tiêu. Hạn chế này đã được khắc phục trong CMake 3.11.