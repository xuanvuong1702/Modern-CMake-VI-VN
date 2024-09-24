## Tải xuống các dự án

### Phương pháp tải xuống: thời gian build

Cho đến CMake 3.11, phương pháp tải xuống chính cho các gói được thực hiện trong thời gian build. Điều này gây ra một số vấn đề; quan trọng nhất là `add_subdirectory` không hoạt động trên một tệp chưa tồn tại! Công cụ tích hợp sẵn cho việc này, ExternalProject, phải giải quyết vấn đề này bằng cách tự thực hiện việc build. (Tuy nhiên, nó cũng có thể build các gói không phải CMake).[^1]

[^1]: Lưu ý rằng ExternalData là công cụ dành cho dữ liệu không phải gói.

### Phương pháp tải xuống: thời gian cấu hình

Nếu bạn thích thời gian cấu hình, hãy xem kho lưu trữ [Crascit/DownloadProject](https://github.com/Crascit/DownloadProject) để có giải pháp thay thế. Tuy nhiên, các submodule hoạt động rất tốt nên tôi đã ngừng hầu hết các lượt tải xuống cho những thứ như GoogleTest và chuyển chúng sang submodule. Tải xuống tự động khó bắt chước hơn nếu bạn không có quyền truy cập internet và chúng thường được triển khai trong thư mục build, lãng phí thời gian và không gian nếu bạn có nhiều thư mục build.


**Giải thích:**

Tài liệu này đề cập đến hai phương pháp tải xuống các dự án trong CMake:

**1. Tải xuống trong thời gian build:**

- Đây là phương pháp cũ, được sử dụng trước CMake 3.11.
- Nó sử dụng công cụ `ExternalProject` để tải xuống và build các dự án bên ngoài.
- Nhược điểm: `add_subdirectory` không hoạt động trên tệp chưa được tải xuống, dẫn đến việc `ExternalProject` phải tự build dự án.

**2. Tải xuống trong thời gian cấu hình:**

- Phương pháp này sử dụng công cụ `DownloadProject` từ kho lưu trữ Crascit/DownloadProject.
- Nó tải xuống các dự án trong quá trình cấu hình CMake, trước khi build.
- Ưu điểm: `add_subdirectory` hoạt động bình thường.
- Tác giả khuyến nghị sử dụng submodule thay vì tải xuống tự động vì submodule dễ quản lý hơn và không phụ thuộc vào kết nối internet.


**Tóm tắt:**

Tài liệu so sánh hai phương pháp tải xuống dự án trong CMake và khuyến nghị sử dụng phương pháp tải xuống trong thời gian cấu hình (hoặc submodule) thay vì phương pháp tải xuống trong thời gian build.


