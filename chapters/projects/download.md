## Tải xuống các Dự án

### Phương thức Tải xuống: Thời điểm Build

Cho đến CMake 3.11, phương thức tải xuống chính cho các gói được thực hiện **tại thời điểm build**. Điều này gây ra một số vấn đề; quan trọng nhất là `add_subdirectory` không hoạt động trên một tệp chưa tồn tại! Công cụ tích hợp sẵn cho việc này, `ExternalProject`, phải giải quyết vấn đề này bằng cách tự thực hiện quá trình build. (Tuy nhiên, nó cũng có thể build các gói không phải CMake).[^1]

[^1]: Lưu ý rằng `ExternalData` là công cụ dành cho dữ liệu không phải gói.

### Phương thức Tải xuống: Thời điểm Cấu hình

Nếu bạn thích **tải xuống tại thời điểm cấu hình**, hãy xem kho lưu trữ [Crascit/DownloadProject](https://github.com/Crascit/DownloadProject) để có một giải pháp thay thế. Tuy nhiên, các submodule hoạt động rất tốt nên tôi đã ngừng hầu hết các lượt tải xuống cho những thứ như GoogleTest và chuyển chúng sang submodule. Tải xuống tự động khó mô phỏng hơn nếu bạn không có quyền truy cập internet và chúng thường được triển khai trong thư mục build, lãng phí thời gian và không gian nếu bạn có nhiều thư mục build. 
