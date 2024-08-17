# Giới Thiệu Về CMake Hiện Đại

Mọi người thường không thích các hệ thống build.
Hãy xem các bài nói chuyện từ CppCon17 để thấy ví dụ về các nhà phát triển biến trạng thái của các hệ thống build thành trò cười.
Điều này đặt ra câu hỏi: Tại sao?
Chắc chắn có rất nhiều vấn đề khi xây dựng.
Nhưng tôi nghĩ rằng, vào năm 2023, chúng ta đã có một giải pháp rất tốt cho khá nhiều vấn đề đó.
Đó là CMake. Nhưng không phải CMake 2.8; phiên bản đó được phát hành trước khi C++11 thậm chí còn chưa tồn tại!
Cũng không phải là những ví dụ tồi tệ về CMake (thậm chí cả những ví dụ được đăng trên danh sách hướng dẫn của KitWare).
Tôi đang nói về CMake Hiện Đại. CMake 3.15+, thậm chí có thể là CMake 3.30+!
Nó sạch sẽ, mạnh mẽ và thanh lịch, vì vậy bạn có thể dành phần lớn thời gian của mình để viết mã, không phải thêm dòng vào một tệp Make (hoặc CMake 2) không thể đọc được và không thể bảo trì.

:::{note}
Bạn có quan tâm đến việc sử dụng CMake để xây dựng các gói Python không? Tôi đang làm việc trên scikit-build-core, [đề xuất được mô tả ở đây][skprop]! Hãy cho tôi biết nếu bạn có một trường hợp sử dụng!

[skprop]: https://iscinumpy.gitlab.io/post/scikit-build-proposal/

:::

:::{attention}

Cuốn sách này được coi là một tài liệu sống. Bạn có thể tạo một vấn đề hoặc gửi yêu cầu hợp nhất trên [GitLab](https://gitlab.com/CLIUtils/modern-cmake).
Bạn cũng có thể [tải xuống bản sao dưới dạng PDF](https://CLIUtils.gitlab.io/modern-cmake/modern-cmake.pdf). Hãy chắc chắn kiểm tra [HSF CMake Training][], nữa nhé!

:::
:::{warning}

Cuốn sách này vừa được chuyển từ GitBook sang JupyterBook. Có thể sẽ có một số lỗi nhỏ sau khi chuyển đổi - hãy thoải mái báo cáo hoặc sửa các vấn đề!

:::

Tóm lại, đây là những câu hỏi có thể bạn đang nghĩ đến nếu bạn đang cân nhắc về CMake Hiện Đại:

## Tại sao tôi cần một hệ thống build tốt?

Bạn có gặp phải bất kỳ điều nào sau đây không?

- Bạn muốn tránh việc hard-code các đường dẫn
- Bạn cần xây dựng một gói trên nhiều máy tính
- Bạn muốn sử dụng CI (tích hợp liên tục)
- Bạn cần hỗ trợ các hệ điều hành khác nhau (có thể chỉ là các biến thể của Unix)
- Bạn muốn hỗ trợ nhiều trình biên dịch
- Bạn muốn sử dụng một IDE, nhưng có thể không phải lúc nào cũng vậy
- Bạn muốn mô tả cách chương trình của bạn được cấu trúc một cách logic, không phải là các cờ và lệnh
- Bạn muốn sử dụng một thư viện
- Bạn muốn sử dụng các công cụ như Clang-Tidy để giúp bạn viết mã
- Bạn muốn sử dụng trình gỡ lỗi

Nếu vậy, bạn sẽ hưởng lợi từ một hệ thống build giống như CMake.
## Tại sao phải chọn CMake?

Hệ thống build là một chủ đề nóng. Tất nhiên có nhiều lựa chọn. Nhưng ngay cả một hệ thống build rất tốt, hoặc một hệ thống sử dụng lại cú pháp quen thuộc, cũng không thể so sánh với CMake.
Tại sao?
Hỗ trợ.
Mọi IDE đều hỗ trợ CMake (hoặc CMake hỗ trợ IDE đó).
Nhiều gói sử dụng CMake hơn bất kỳ hệ thống nào khác.
Vì vậy, nếu bạn sử dụng một thư viện được thiết kế để bao gồm trong mã của bạn, bạn có một lựa chọn: Tự tạo hệ thống build của riêng bạn, hoặc sử dụng một trong những hệ thống build có sẵn, và điều đó hầu như luôn bao gồm CMake.
Và điều đó sẽ nhanh chóng trở thành mẫu số chung nếu bạn bao gồm nhiều dự án.
Và, nếu bạn cần một thư viện đã được cài đặt sẵn, khả năng cao là nó sẽ có một script tìm CMake hoặc script cấu hình CMake.

## Tại sao sử dụng CMake Hiện Đại?

Khoảng CMake 2.6-2.8, CMake bắt đầu chiếm lĩnh. Nó có mặt trong hầu hết các trình quản lý gói cho hệ điều hành Linux, và được sử dụng trong nhiều gói.

Sau đó Python 3 ra đời.

Tôi biết, điều này không liên quan gì đến CMake.

Nhưng nó có số 3.
Và nó theo sau số 2.
Và đó là một quá trình chuyển đổi khó khăn, xấu xí mà vẫn đang tiếp diễn ở một số nơi, thậm chí đến ngày nay.

Tôi tin rằng CMake 3 đã gặp xui xẻo khi theo sau Python 3.[^1]
Mặc dù mọi phiên bản của CMake đều tương thích ngược một cách điên rồ, nhưng loạt phiên bản 3 lại bị coi như một thứ gì đó mới mẻ.
Và vì vậy, bạn sẽ thấy các hệ điều hành như CentOS7 với GCC 4.8, với hỗ trợ C++14 gần như hoàn chỉnh, và CMake 2.8, được phát hành nhiều năm trước khi C++11 ra đời.

Bạn thực sự nên _ít nhất_ sử dụng một phiên bản CMake ra đời sau trình biên dịch của bạn, vì nó cần biết các cờ trình biên dịch, v.v., cho phiên bản đó.
Và, vì CMake sẽ tự hạ cấp xuống phiên bản tối thiểu được yêu cầu trong tệp CMake của bạn, việc cài đặt một CMake mới, thậm chí trên toàn hệ thống, là khá an toàn.
Bạn nên _ít nhất_ cài đặt nó cục bộ.
Nó dễ dàng (1-2 dòng trong nhiều trường hợp), và bạn sẽ thấy rằng 5 phút làm việc sẽ tiết kiệm cho bạn hàng trăm dòng và giờ viết `CMakeLists.txt`, và sẽ dễ dàng bảo trì hơn trong dài hạn.

Cuốn sách này cố gắng giải quyết vấn đề về các ví dụ kém và các thực hành tốt nhất mà bạn sẽ tìm thấy tràn lan trên web.

# Mục lục

- [Giới thiệu về CMake Hiện Đại](README.md)
  - [Cài đặt CMake](chapters/intro/installing.md)
  - [Chạy CMake](chapters/intro/running.md)
  - [Những điều nên và không nên làm](chapters/intro/dodonot.md)
  - [Có gì mới trong CMake](chapters/intro/newcmake.md)
- [Giới thiệu về những điều cơ bản](chapters/basics.md)
  - [Biến và Bộ nhớ đệm](chapters/basics/variables.md)
  - [Lập trình trong CMake](chapters/basics/functions.md)
  - [Giao tiếp với mã của bạn](chapters/basics/comms.md)
  - [Cách cấu trúc dự án của bạn](chapters/basics/structure.md)
  - [Chạy các chương trình khác](chapters/basics/programs.md)
  - [Một ví dụ đơn giản](chapters/basics/example.md)
- [Thêm tính năng](chapters/features.md)
  - [C++11 và các phiên bản sau](chapters/features/cpp11.md)
  - [Các nhu cầu nhỏ nhưng phổ biến](chapters/features/small.md)
  - [Tiện ích](chapters/features/utilities.md)
  - [Các module hữu ích](chapters/features/modules.md)
  - [IDE](chapters/features/ides.md)
  - [Gỡ lỗi](chapters/features/debug.md)
- [Bao gồm các dự án](chapters/projects.md)
  - [Submodule](chapters/projects/submodule.md)
  - [DownloadProject](chapters/projects/download.md)
  - [Fetch (CMake 3.11)](chapters/projects/fetch.md)
- [Kiểm thử](chapters/testing.md)
  - [GoogleTest](chapters/testing/googletest.md)
  - [Catch](chapters/testing/catch.md)
- [Xuất và Cài đặt](chapters/install.md)
  - [Cài đặt](chapters/install/installing.md)
  - [Xuất](chapters/install/exporting.md)
  - [Đóng gói](chapters/install/packaging.md)
- [Tìm kiếm thư viện (Gói)](chapters/packages.md)
  - [CUDA](chapters/packages/CUDA.md)
  - [OpenMP](chapters/packages/OpenMP.md)
  - [Boost](chapters/packages/Boost.md)
  - [MPI](chapters/packages/MPI.md)
  - [ROOT](chapters/packages/ROOT.md)
    - [Ví dụ UseFile](examples/root-usefile/README.md)
    - [Ví dụ đơn giản](examples/root-simple/README.md)
    - [Ví dụ Dictionary](examples/root-dict/README.md)
  - [Minuit2](chapters/packages/Minuit2.md)

<!--
* [Tải xuống PDF](ref://modern-cmake.pdf)
* [Duyệt mã nguồn](https://gitlab.com/CLIUtils/modern-cmake) -->

# Các nguồn khác

Các tài liệu khác từ tác giả gốc của cuốn sách này:

- [HSF CMake Training][]
- [Các bài nói chuyện về CMake Hiện Đại Tương Tác](https://gitlab.com/CLIUtils/modern-cmake-interactive-talk)

Có một số nơi khác để tìm thông tin tốt trên web. Dưới đây là một số trong số đó:

- [Trợ giúp chính thức](https://cmake.org/cmake/help/latest/): Tài liệu thực sự tuyệt vời. Được tổ chức tốt, tìm kiếm tuyệt vời, và bạn có thể chuyển đổi phiên bản ở trên cùng. Nó chỉ không có một "hướng dẫn thực hành tốt nhất" tuyệt vời, đó là điều mà cuốn sách này cố gắng bổ sung.
- [Effective Modern CMake](https://gist.github.com/mbinna/c61dbb39bca0e4fb7d1f73b0d66a4fd1): Một danh sách tuyệt vời về những điều nên và không nên làm.
- [Embracing Modern CMake](https://steveire.wordpress.com/2017/11/05/embracing-modern-cmake/): Một bài viết với mô tả tốt về thuật ngữ
- [It's time to do CMake Right](https://pabloariasal.github.io/2018/02/19/its-time-to-do-cmake-right/): Một tập hợp các thực hành tốt nhất cho các dự án CMake Hiện Đại.
- [The Ultimate Guide to Modern CMake](https://rix0r.nl/blog/2015/08/13/cmake-guide/): Một bài viết hơi cũ với ý định tương tự.
- [More Modern CMake](https://youtu.be/y7ndUhdQuU8): Một bài thuyết trình tuyệt vời từ Meeting C++ 2018 khuyến nghị CMake 3.12+. Bài nói này gọi CMake 3.0+ là "CMake Hiện Đại" và CMake 3.12+ là "CMake Hiện Đại Hơn".
- [Oh No! More Modern CMake](https://www.youtube.com/watch?v=y9kSr5enrSk): Phần tiếp theo của More Modern CMake.
- [toeb/moderncmake](https://github.com/toeb/moderncmake): Một bài thuyết trình và các ví dụ hay về CMake 3.5+, với giới thiệu về cú pháp thông qua tổ chức dự án.
## Lời cảm ơn

Modern CMake ban đầu được viết bởi [Henry Schreiner](https://iscinumpy.gitlab.io). Các cộng tác viên khác có thể được tìm thấy [được liệt kê trên GitLab](https://gitlab.com/CLIUtils/modern-cmake/-/network/master).

[hsf cmake training]: https://hsf-training.github.io/hsf-training-cmake-webpage/01-intro/index.html

[^1]: CMake 3.0 cũng đã loại bỏ một số tính năng đã lỗi thời từ các phiên bản rất cũ của CMake và thực hiện một thay đổi rất nhỏ không tương thích ngược liên quan đến cú pháp dấu ngoặc vuông, vì vậy điều này không hoàn toàn công bằng; có thể có một số tệp CMake rất, rất cũ sẽ ngừng hoạt động với phiên bản 3. Tuy nhiên, tôi chưa từng thấy trường hợp nào như vậy.