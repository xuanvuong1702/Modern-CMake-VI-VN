## Giới thiệu về CMake Hiện đại

Mọi người đều thích ghét hệ thống build.
Chỉ cần xem các buổi nói chuyện từ CppCon 2017 để thấy ví dụ về các nhà phát triển lấy hệ thống build ra làm trò đùa.
Điều này đặt ra câu hỏi: Tại sao?
Chắc chắn có rất nhiều vấn đề khi build.
Nhưng tôi nghĩ rằng, vào năm 2023, chúng ta có một giải pháp rất tốt cho khá nhiều vấn đề đó.
Đó là CMake. Nhưng không phải CMake 2.8; phiên bản đó được phát hành trước cả khi C++11 tồn tại!
Cũng không phải những ví dụ khủng khiếp về CMake ngoài kia (ngay cả những ví dụ được đăng trên danh sách hướng dẫn của chính KitWare).
Tôi đang nói về CMake hiện đại. CMake 3.15+, thậm chí có thể là CMake 3.30+!
Nó gọn gàng, mạnh mẽ và thanh lịch, vì vậy bạn có thể dành phần lớn thời gian để lập trình, thay vì thêm dòng vào tệp Make (hoặc CMake 2) không thể đọc được và không thể bảo trì.

:::{note}

Bạn có quan tâm đến việc sử dụng CMake để build các gói Python không? Tôi đang làm việc trên scikit-build-core, [đề xuất được mô tả ở đây][skprop]! Hãy cho tôi biết nếu bạn có một trường hợp sử dụng!

[skprop]: https://iscinumpy.gitlab.io/post/scikit-build-proposal/

:::

:::{attention}

Cuốn sách này được coi là một tài liệu sống. Bạn có thể tạo issue hoặc gửi yêu cầu hợp nhất trên [GitLab](https://gitlab.com/CLIUtils/modern-cmake).
Bạn cũng có thể [tải xuống bản sao dưới dạng PDF](https://CLIUtils.gitlab.io/modern-cmake/modern-cmake.pdf). Hãy nhớ xem [Khóa huấn luyện CMake của HSF][], nữa nhé!

:::

:::{warning}

Cuốn sách này vừa được chuyển từ GitBook sang JupyterBook. Có thể có một số điểm chưa hoàn thiện sau khi chuyển đổi - hãy thoải mái báo cáo hoặc sửa lỗi!

:::

Tóm lại, đây là những câu hỏi có khả năng nhất trong tâm trí bạn nếu bạn đang cân nhắc CMake hiện đại:

## Tại sao tôi cần một hệ thống build tốt?

Có điều nào sau đây áp dụng cho bạn không?

- Bạn muốn tránh mã hóa cứng các đường dẫn
- Bạn cần build một gói trên nhiều hơn một máy tính
- Bạn muốn sử dụng CI (tích hợp liên tục)
- Bạn cần hỗ trợ các hệ điều hành khác nhau (thậm chí có thể chỉ là các bản phân phối của Unix)
- Bạn muốn hỗ trợ nhiều trình biên dịch
- Bạn muốn sử dụng IDE, nhưng có thể không phải lúc nào cũng vậy
- Bạn muốn mô tả cấu trúc logic của chương trình, không phải là các flag và lệnh
- Bạn muốn sử dụng một thư viện
- Bạn muốn sử dụng các công cụ, như Clang-Tidy, để hỗ trợ việc lập trình
- Bạn muốn sử dụng trình gỡ lỗi

Nếu vậy, bạn sẽ được hưởng lợi từ một hệ thống build giống CMake.

## Tại sao câu trả lời phải là CMake?

Hệ thống build là một chủ đề nóng. Tất nhiên có rất nhiều lựa chọn. Nhưng ngay cả một hệ thống thực sự tốt, hoặc một hệ thống sử dụng lại cú pháp quen thuộc, cũng không thể so sánh với CMake.
Tại sao?
Hỗ trợ.
Mọi IDE đều hỗ trợ CMake (hoặc CMake hỗ trợ IDE đó).
Có nhiều gói sử dụng CMake hơn bất kỳ hệ thống nào khác.
Vì vậy, nếu bạn sử dụng một thư viện được thiết kế để đưa vào mã của mình, bạn có một lựa chọn: Tự tạo hệ thống build của riêng mình hoặc sử dụng một trong những hệ thống được cung cấp và điều đó hầu như luôn bao gồm CMake.
Và đó sẽ nhanh chóng trở thành mẫu số chung nếu bạn bao gồm nhiều dự án.
Và, nếu bạn cần một thư viện được cài đặt sẵn, khả năng nó có tập lệnh tìm kiếm CMake hoặc tập lệnh cấu hình CMake là rất cao.

## Tại sao nên sử dụng CMake hiện đại?

Vào khoảng CMake 2.6-2.8, CMake bắt đầu chiếm ưu thế. Nó có trong hầu hết các trình quản lý gói cho các hệ điều hành Linux và đang được sử dụng trong rất nhiều gói.

Sau đó, Python 3 ra đời.

Tôi biết, điều này dường như không liên quan gì đến CMake.

Nhưng nó có số 3.
Và nó theo sau số 2.
Và đó là một quá trình chuyển đổi khó khăn, xấu xí, vẫn đang diễn ra ở một số nơi, ngay cả ngày nay.

Tôi tin rằng CMake 3 đã không may mắn khi ra mắt sau Python 3.[^1]
Mặc dù mọi phiên bản của CMake đều tương thích ngược một cách đáng kinh ngạc, nhưng loạt 3 vẫn bị coi như một thứ gì đó mới mẻ.
Và vì vậy, bạn sẽ tìm thấy các hệ điều hành như CentOS 7 với GCC 4.8, với hỗ trợ C++14 gần như hoàn chỉnh và CMake 2.8, ra mắt nhiều năm trước C++11.

Bạn thực sự nên _ít nhất_ sử dụng phiên bản CMake ra mắt sau trình biên dịch của mình, vì nó cần biết các flag của trình biên dịch, v.v., cho phiên bản đó.
Và, vì CMake sẽ tự động hạ cấp xuống phiên bản tối thiểu cần thiết trong tệp CMake của bạn, nên việc cài đặt CMake mới, ngay cả trên toàn hệ thống, là khá an toàn.
Bạn _ít nhất_ nên cài đặt nó cục bộ.
Nó rất dễ dàng (1-2 dòng trong nhiều trường hợp) và bạn sẽ thấy rằng 5 phút làm việc sẽ giúp bạn tiết kiệm hàng trăm dòng và hàng giờ viết `CMakeLists.txt` và sẽ dễ bảo trì hơn về lâu dài.

Cuốn sách này cố gắng giải quyết vấn đề về các ví dụ kém và các phương pháp hay nhất mà bạn sẽ thấy tràn lan trên web.

## Các nguồn khác

Các tài liệu khác từ tác giả ban đầu của cuốn sách này:

- [Khóa huấn luyện CMake của HSF][]
- [Các buổi nói chuyện CMake hiện đại tương tác](https://gitlab.com/CLIUtils/modern-cmake-interactive-talk)

Có một số nơi khác để tìm thông tin tốt trên web. Dưới đây là một số trong số đó:

- [Tài liệu trợ giúp chính thức](https://cmake.org/cmake/help/latest/): Tài liệu thực sự tuyệt vời. Được tổ chức tốt, tìm kiếm tuyệt vời và bạn có thể chuyển đổi phiên bản ở trên cùng. Nó chỉ không có một "hướng dẫn thực hành tốt nhất" tuyệt vời, đó là điều mà cuốn sách này cố gắng lấp đầy.
- [CMake hiện đại hiệu quả](https://gist.github.com/mbinna/c61dbb39bca0e4fb7d1f73b0d66a4fd1): Một danh sách tuyệt vời về những việc nên làm và không nên làm.
- [Chấp nhận CMake hiện đại](https://steveire.wordpress.com/2017/11/05/embracing-modern-cmake/): Một bài đăng với mô tả hay về thuật ngữ
- [Đã đến lúc làm CMake đúng cách](https://pabloariasal.github.io/2018/02/19/its-time-to-do-cmake-right/): Một bộ phương pháp hay nhất đẹp mắt cho các dự án CMake hiện đại.
- [Hướng dẫn cơ bản về CMake hiện đại](https://rix0r.nl/blog/2015/08/13/cmake-guide/): Một bài đăng hơi cũ với mục đích tương tự.
- [CMake hiện đại hơn nữa](https://youtu.be/y7ndUhdQuU8): Một bài thuyết trình tuyệt vời từ Meeting C++ 2018 khuyên dùng CMake 3.12+. Bài nói chuyện này gọi CMake 3.0+ là "CMake hiện đại" và CMake 3.12+ là "CMake hiện đại hơn nữa".
- [Ôi không! CMake hiện đại hơn nữa](https://www.youtube.com/watch?v=y9kSr5enrSk): Phần tiếp theo của CMake hiện đại hơn nữa.
- [toeb/moderncmake](https://github.com/toeb/moderncmake): Một bài thuyết trình và các ví dụ hay về CMake 3.5+, với phần giới thiệu về cú pháp thông qua tổ chức dự án

## Tín dụng

CMake hiện đại ban đầu được viết bởi [Henry Schreiner](https://iscinumpy.gitlab.io). Những người đóng góp khác có thể được tìm thấy [được liệt kê trên GitLab](https://gitlab.com/CLIUtils/modern-cmake/-/network/master).

[Khóa huấn luyện CMake của HSF]: https://hsf-training.github.io/hsf-training-cmake-webpage/01-intro/index.html

[^1]: CMake 3.0 cũng đã loại bỏ một số tính năng không được dùng nữa từ các phiên bản CMake rất cũ và thực hiện một thay đổi rất nhỏ không tương thích ngược về cú pháp liên quan đến dấu ngoặc vuông, vì vậy điều này không hoàn toàn công bằng; có thể có một số tệp CMake rất, rất cũ sẽ ngừng hoạt động với 3. Tuy nhiên, tôi chưa bao giờ thấy một tệp nào.
