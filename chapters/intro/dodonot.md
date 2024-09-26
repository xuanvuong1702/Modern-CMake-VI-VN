# Nên làm và Không nên làm

Hai danh sách tiếp theo chủ yếu dựa trên gist tuyệt vời [Effective Modern CMake]. Danh sách đó dài hơn và chi tiết hơn nhiều, bạn cũng có thể đọc nó.

## Các Hành động Cần Tránh khi Sử dụng CMake

- **Không sử dụng các hàm toàn cục**: Điều này bao gồm `link_directories`, `include_libraries` và các hàm tương tự.
- **Không thêm các yêu cầu PUBLIC không cần thiết**: Bạn nên tránh áp đặt điều gì đó lên người dùng mà không cần thiết (`-Wall`). Hãy đặt chúng là PRIVATE.
- **Không sử dụng GLOB cho các tệp**: Make hoặc công cụ khác sẽ không biết nếu bạn thêm tệp mà không chạy lại CMake. Lưu ý rằng CMake 3.12 bổ sung cờ `CONFIGURE_DEPENDS` giúp cải thiện đáng kể điều này nếu bạn cần sử dụng nó.
- **Liên kết trực tiếp tới các tệp đã build**: Luôn liên kết tới các target nếu có thể.
- **Không bao giờ bỏ qua PUBLIC/PRIVATE khi liên kết**: Điều này khiến tất cả các liên kết trong tương lai không có từ khóa.

## Các Khuôn Mẫu CMake

- **Coi CMake là mã**: Nó là mã. Nó nên sạch sẽ và dễ đọc như mọi đoạn mã khác.
- **Suy nghĩ theo target**: Các target của bạn nên thể hiện các khái niệm. Tạo một target INTERFACE (IMPORTED) cho bất cứ thứ gì nên được giữ cùng nhau và liên kết tới target đó.
- **Xuất interface của bạn**: Bạn nên có thể chạy từ bản build hoặc cài đặt.
- **Viết tệp Config.cmake**: Đây là điều mà tác giả thư viện nên làm để hỗ trợ người dùng.
- **Tạo các target ALIAS để giữ cho việc sử dụng nhất quán**: Sử dụng `add_subdirectory` và `find_package` nên cung cấp các target và namespace giống nhau.
- **Kết hợp các chức năng phổ biến thành các hàm hoặc macro được ghi chép rõ ràng**: Hàm thường tốt hơn.
- **Sử dụng tên hàm viết thường**: Các hàm và macro CMake có thể được gọi bằng chữ thường hoặc chữ hoa. Luôn sử dụng chữ thường. Chữ hoa dành cho biến.
- **Sử dụng `cmake_policy` và/hoặc phạm vi phiên bản**: Các chính sách thay đổi vì một lý do. Chỉ đặt các chính sách OLD một cách riêng lẻ nếu bạn buộc phải làm như vậy.

## Chọn Phiên Bản Tối Thiểu vào Năm 2024:

Bạn nên _chạy_ CMake tối thiểu nào cục bộ và mức tối thiểu nào bạn nên _hỗ trợ_ cho những người sử dụng mã của bạn? Vì bạn đang đọc bài viết này, bạn sẽ có thể nhận được bản phát hành trong một vài phiên bản CMake gần đây nhất; hãy làm điều đó, nó sẽ giúp việc phát triển của bạn dễ dàng hơn. Để được hỗ trợ, có hai cách để chọn mức tối thiểu: dựa trên các tính năng được bổ sung (điều mà nhà phát triển quan tâm) hoặc dựa trên các CMake được cài đặt sẵn phổ biến (điều mà người dùng quan tâm).

Không bao giờ chọn phiên bản tối thiểu cũ hơn phiên bản trình biên dịch cũ nhất mà bạn hỗ trợ. CMake phải luôn mới ít nhất bằng trình biên dịch của bạn.

### Chọn Phiên Bản Tối Thiểu Nào - Hỗ Trợ Hệ Điều Hành:

- 3.16: Ubuntu 20.04.
- 3.17: Amazon Linux
- 3.19: Phiên bản đầu tiên hỗ trợ Apple Silicon.
- 3.20: CentOS 8.
- 3.22: Ubuntu 22.04.
- 3.25: Debian 12 (11 backports)
- 3.28: Ubuntu 24.04.
- 3.29: Debian 13 (12 backports)
- mới nhất: pip/conda-forge/homebrew/chocolaty, v.v.

### Chọn Phiên Bản Tối Thiểu Nào - Tính Năng:

- 3.11: Cài đặt `IMPORTED INTERFACE`, nhanh hơn, FetchContent, `COMPILE_LANGUAGE` trong IDE
- 3.12: C++20, `cmake --build build -j N`, `SHELL:`, FindPython
- 3.14/3.15: CLI, cập nhật FindPython
- 3.16: Unity builds / precompiled headers, các tính năng meta CUDA
- 3.17/3.18: Nhiều CUDA hơn, lập trình meta, cập nhật FindPython
- 3.19: Presets
- 3.20: C++23, `cmake_path`
- 3.24: Trình tìm kiếm package
- 3.25: Khối lệnh để phân định phạm vi
- 3.28: Module C++20
- 3.29: Build target trước khi test target
- 3.30: Hỗ trợ đầy đủ C++26

[effective modern cmake]: https://gist.github.com/mbinna/c61dbb39bca0e4fb7d1f73b0d66a4fd1
