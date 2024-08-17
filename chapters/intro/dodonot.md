# Những điều nên và không nên làm

Hai danh sách tiếp theo được dựa trên gist tuyệt vời [Effective Modern CMake]. Danh sách đó dài hơn và chi tiết hơn nhiều, bạn có thể đọc thêm nếu muốn.

## Các mẫu CMake không nên làm

- **Không sử dụng các hàm toàn cục**: Bao gồm `link_directories`, `include_libraries`, và các hàm tương tự.
- **Không thêm các yêu cầu PUBLIC không cần thiết**: Bạn nên tránh ép buộc người dùng sử dụng những thứ không cần thiết (`-Wall`). Hãy làm chúng PRIVATE thay thế.
- **Không sử dụng GLOB để lấy tệp**: Make hoặc công cụ khác sẽ không biết nếu bạn thêm tệp mà không chạy lại CMake. Lưu ý rằng CMake 3.12 thêm cờ `CONFIGURE_DEPENDS` giúp cải thiện điều này nếu bạn cần sử dụng.
- **Liên kết trực tiếp đến các tệp đã xây dựng**: Luôn liên kết đến các mục tiêu nếu có sẵn.
- **Không bao giờ bỏ qua PUBLIC/PRIVATE khi liên kết**: Điều này sẽ khiến tất cả các liên kết trong tương lai không có từ khóa.
## Mẫu CMake

- **Xem CMake như mã nguồn**: Nó là mã nguồn. Nó cần phải sạch sẽ và dễ đọc như tất cả các mã nguồn khác.
- **Suy nghĩ theo mục tiêu**: Các mục tiêu của bạn nên đại diện cho các khái niệm. Tạo một mục tiêu INTERFACE (IMPORTED) cho bất cứ thứ gì cần giữ nguyên và liên kết với nó.
- **Xuất giao diện của bạn**: Bạn nên có thể chạy từ build hoặc install.
- **Viết tệp Config.cmake**: Đây là điều mà một tác giả thư viện nên làm để hỗ trợ khách hàng.
- **Tạo các mục tiêu ALIAS để giữ cho việc sử dụng nhất quán**: Sử dụng `add_subdirectory` và `find_package` nên cung cấp cùng các mục tiêu và không gian tên.
- **Kết hợp các chức năng chung vào các hàm hoặc macro được ghi chú rõ ràng**: Thường thì hàm sẽ tốt hơn.
- **Sử dụng tên hàm viết thường**: Các hàm và macro của CMake có thể được gọi bằng chữ thường hoặc chữ hoa. Luôn sử dụng chữ thường. Chữ hoa dành cho biến.
- **Sử dụng `cmake_policy` và/hoặc phạm vi phiên bản**: Chính sách thay đổi vì lý do. Chỉ thiết lập từng phần chính sách OLD nếu bạn phải làm vậy.

## Chọn phiên bản tối thiểu vào năm 2024:

Phiên bản CMake tối thiểu nào bạn nên _chạy_ cục bộ, và phiên bản tối thiểu nào bạn nên _hỗ trợ_ cho người dùng mã nguồn của bạn? Vì bạn đang đọc điều này, bạn nên có thể lấy một phiên bản phát hành trong vài phiên bản gần đây của CMake; làm điều đó, nó sẽ làm cho việc phát triển của bạn dễ dàng hơn. Để hỗ trợ, có hai cách để chọn phiên bản tối thiểu: dựa trên các tính năng được thêm vào (điều mà nhà phát triển quan tâm), hoặc trên các phiên bản CMake được cài đặt sẵn phổ biến (điều mà người dùng quan tâm).

Không bao giờ chọn phiên bản tối thiểu cũ hơn phiên bản trình biên dịch cũ nhất mà bạn hỗ trợ. CMake nên luôn luôn mới ít nhất bằng trình biên dịch của bạn.
### Phiên bản tối thiểu nên chọn - Hỗ trợ hệ điều hành:

- 3.16: Ubuntu 20.04.
- 3.17: Amazon Linux
- 3.19: Hỗ trợ đầu tiên cho Apple Silicon.
- 3.20: CentOS 8.
- 3.22: Ubuntu 22.04.
- 3.25: Debian 12 (backports từ 11)
- 3.28: Ubuntu 24.04.
- 3.29: Debian 13 (backports từ 12)
- mới nhất: pip/conda-forge/homebrew/chocolaty, v.v.

### Phiên bản tối thiểu nên chọn - Tính năng:

- 3.11: Thiết lập `IMPORTED INTERFACE`, nhanh hơn, FetchContent, `COMPILE_LANGUAGE` trong IDEs
- 3.12: C++20, `cmake --build build -j N`, `SHELL:`, FindPython
- 3.14/3.15: CLI, cập nhật FindPython
- 3.16: Unity builds / precompiled headers, tính năng meta CUDA
- 3.17/3.18: Nhiều tính năng CUDA hơn, metaprogramming, cập nhật FindPython
- 3.19: Presets
- 3.20: C++23, `cmake_path`
- 3.24: Trình tìm kiếm gói
- 3.25: Blocks cho phạm vi
- 3.28: Các module C++20
- 3.29: Build trước khi kiểm tra mục tiêu
- 3.30: Hỗ trợ đầy đủ C++26

[effective modern cmake]: https://gist.github.com/mbinna/c61dbb39bca0e4fb7d1f73b0d66a4fd1