## Minuit2

Minuit2 có sẵn ở chế độ độc lập, để sử dụng trong các trường hợp mà ROOT không có sẵn hoặc không được biên dịch với Minuit2 được kích hoạt. Phần này sẽ đề cập đến các cách sử dụng được khuyến nghị, cũng như một số khía cạnh của thiết kế.

## Cách sử dụng

Minuit2 có thể được sử dụng theo bất kỳ cách CMake tiêu chuẩn nào, từ mã nguồn ROOT hoặc từ bản phân phối mã nguồn độc lập:

```cmake
# Kiểm tra Minuit2 trong ROOT nếu bạn muốn
# và sau đó liên kết đến ROOT::Minuit2 thay thế

add_subdirectory(minuit2) # hoặc root/math/minuit2
# HOẶC
find_package(Minuit2 CONFIG) # Hoặc biên dịch hoặc cài đặt

target_link_libraries(MyProgram PRIVATE Minuit2::Minuit2)
```

## Phát triển

Minuit2 là một ví dụ điển hình về các giải pháp tiềm năng cho vấn đề tích hợp bản build hiện đại (CMake 3.1+) vào một framework hiện có.

Để xử lý hai hệ thống CMake khác nhau, tệp `CMakeLists.txt` chính định nghĩa các tùy chọn chung, sau đó gọi tệp `Standalone.cmake` nếu đây không phải là bản build như một phần của ROOT.

Phần khó nhất trong trường hợp ROOT là Minuit2 yêu cầu các tệp nằm ngoài thư mục `math/minuit2`. Điều này đã được giải quyết bằng cách thêm tệp `copy_standalone.cmake` với một hàm nhận danh sách tên tệp và sau đó trả về danh sách tên tệp tại chỗ trong mã nguồn gốc, hoặc sao chép tệp vào mã nguồn cục bộ và trả về danh sách các vị trí mới, hoặc chỉ trả về danh sách các vị trí mới nếu mã nguồn gốc không tồn tại (độc lập).

```bash
# Sao chép tệp vào thư mục mã nguồn
cmake /root/math/minuit2 -Dminuit2-standalone=ON

# Tạo .tar.gz từ thư mục mã nguồn
make package_source

# Tùy chọn, dọn dẹp thư mục mã nguồn
make purge
```

Điều này chỉ dành cho các nhà phát triển muốn tạo ra các gói mã nguồn - một người dùng bình thường _không truyền tùy chọn này_ và sẽ không tạo bản sao mã nguồn.

Bạn có thể sử dụng `make install` hoặc `make package` (các gói nhị phân) mà không cần thêm tùy chọn `standalone` này, từ bên trong mã nguồn ROOT hoặc từ một gói độc lập.

[minuit2]: https://root.cern.ch

