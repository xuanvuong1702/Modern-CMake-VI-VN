# Có gì mới trong CMake

Đây là phiên bản rút gọn của changelog CMake với những điểm nổi bật dành cho tác giả. Tên cho mỗi phiên bản được chọn ngẫu nhiên bởi tác giả.

## [CMake đang phát triển][cmake master]: Đang thực hiện

- Hỗ trợ LFortan
- `$comment` được hỗ trợ trong các tệp preset
- `cmake -LR <regex>` để tìm kiếm trong cache

## [CMake 3.30][]: C++26

Phiên bản này thêm hỗ trợ C++26 và cách để lấy tiêu chuẩn mới nhất được hỗ trợ cho một trình biên dịch. Phiên bản này thực hiện nhiều thay đổi nhỏ không được liệt kê dưới đây, như hỗ trợ TLS tốt hơn, một số cập nhật biểu thức generator, và một số cập nhật schema.

- Phát hành lần đầu [2 tháng 7, 2024](https://www.kitware.com/cmake-3-30-0-available-for-download/)
- Một biểu thức generator `$<QUOTE>` được thêm vào để tạo ra `"`
- Hỗ trợ tính năng biên dịch C++26 được thực hiện đầy đủ (một phần từ 3.25)
- `CMAKE_<LANG>_STANDARD_LATEST` giữ tiêu chuẩn mới nhất mà trình biên dịch hiện tại hỗ trợ
- Hỗ trợ Python 3.13+ không khóa luồng
- Hỗ trợ tốt hơn (biến và mục tiêu mới) cho các bản build debug của Windows Python, và `DEBUG_POSTFIX` hiện được thêm bởi `python_add_library`
- FindBoost bị loại bỏ
- Hỗ trợ Visual Studio 2008 bị loại bỏ

# Có gì mới trong CMake

Đây là phiên bản rút gọn của changelog CMake với những điểm nổi bật dành cho tác giả. Tên cho mỗi phiên bản được chọn ngẫu nhiên bởi tác giả.

## [CMake đang phát triển][cmake master]: Đang thực hiện

- Hỗ trợ LFortan
- `$comment` được hỗ trợ trong các tệp preset
- `cmake -LR <regex>` để tìm kiếm trong cache

## [CMake 3.30][]: C++26

Phiên bản này thêm hỗ trợ C++26 và cách để lấy tiêu chuẩn mới nhất được hỗ trợ cho một trình biên dịch. Phiên bản này thực hiện nhiều thay đổi nhỏ không được liệt kê dưới đây, như hỗ trợ TLS tốt hơn, một số cập nhật biểu thức generator, và một số cập nhật schema.

- Phát hành lần đầu [2 tháng 7, 2024](https://www.kitware.com/cmake-3-30-0-available-for-download/)
- Một biểu thức generator `$<QUOTE>` được thêm vào để tạo ra `"`
- Hỗ trợ tính năng biên dịch C++26 được thực hiện đầy đủ (một phần từ 3.25)
- `CMAKE_<LANG>_STANDARD_LATEST` giữ tiêu chuẩn mới nhất mà trình biên dịch hiện tại hỗ trợ
- Hỗ trợ Python 3.13+ không khóa luồng
- Hỗ trợ tốt hơn (biến và mục tiêu mới) cho các bản build debug của Windows Python, và `DEBUG_POSTFIX` hiện được thêm bởi `python_add_library`
- FindBoost bị loại bỏ
- Hỗ trợ Visual Studio 2008 bị loại bỏ
## [CMake 3.25][]: Blocks và SYSTEM

CMake có các lệnh phạm vi khối mới để kiểm soát biến và chính sách một cách chọn lọc. Nó cũng có nhiều kiểm soát hơn đối với SYSTEM. Các tính năng chức năng của CMake được giới thiệu vài phiên bản trước giờ có thể sử dụng trong các lệnh `find_` với `VALIDATOR`. Các luồng công việc cũng được nâng cấp.

- Phát hành lần đầu [16 tháng 11, 2022](https://www.kitware.com/cmake-3-25-0-available-for-download/)
- Hỗ trợ C++26
- LTO cho CUDA với nvcc
- Thêm các preset luồng công việc, cũng như preset gói.
- `SYSTEM` được thêm vào `add_subdirectory`, `FetchContent`, và như một thuộc tính thư mục.
- `block()`/`endblock()` cho phạm vi chính sách/biến, cũng như `PROPOGATE` trong `return()`
- Thêm các biến `BSD` & `LINUX`
- Hàm `VALIDATOR` cho các lệnh `find_*`.
- Nhiều cải tiến cho các lệnh `try_*`.
- Thuộc tính mục tiêu/thư mục `SYSTEM` và `EXPORT_NO_SYSTEM` được thêm vào, cũng cho FetchContent.

## [CMake 3.24][]: Trình tìm gói

Đây là một phiên bản tuyệt vời. Các nhà viết gói sẽ có tích hợp giữa `find_package` và `FetchContent` cho phép các luồng công việc "tải xuống nếu thiếu", và có thể cấu hình bởi các nhà đóng gói. Tương tự, cảnh báo dưới dạng lỗi có thể được đặt bởi một gói và loại bỏ bởi các nhà đóng gói (vẫn đảm bảo không làm điều này trừ khi bạn đang được xây dựng như dự án chính!).

- Phát hành lần đầu [4 tháng 8, 2022](https://blog.kitware.com/cmake-3-24-0-is-available-for-download/)
- Tùy chọn `--fresh` loại bỏ cache cũ khi chạy.
- `find_package` và `FetchContent` hiện có tích hợp - bạn có tùy chọn tải xuống các phụ thuộc thiếu.
- `find_package` có tùy chọn `GLOBAL` mới.
- `CMAKE_PROJECT_TOP_LEVEL_INCLUDES` cho phép người dùng (như các nhà đóng gói) chèn mã trước dự án.
- Quản lý `PATH` cho các biểu thức generator.
- Thêm biến môi trường và biến `CMAKE_COLOR_DIAGNOSTICS`, thay thế `CMAKE_COLOR_MAKEFILE`.
- Bạn có thể vô hiệu hóa `find_*` tìm kiếm trong thư mục cài đặt.
- Thuộc tính `COMPILE_WARNING_AS_ERROR` và biến `CMAKE_`, và `--compile-no-warning-as-error` để vô hiệu hóa nó.
- CUDA hỗ trợ `native` để biên dịch cho các GPU hiện tại được phát hiện.
- Bao gồm `SYSTEM` hiện được tôn trọng trên các generator MSVC.
- Hỗ trợ tốt hơn cho MSVC, XCode, và các công cụ khác.
- Hỗ trợ trình biên dịch `LLVMFlang`.
## [CMake 3.23][]: Thư viện chỉ có header

Một phiên bản vững chắc tập trung vào các thư viện chỉ có header, kiểm soát người dùng nhiều hơn, các preset của CMake, và hỗ trợ CUDA tốt hơn. Có một số tính năng mới mạnh mẽ cho các thư viện chỉ có header, như các thuộc tính mục tiêu `*_SETS` khác nhau. Có các kiểm soát mới như khả năng giới hạn đường dẫn cho các lệnh `find_` và khả năng loại bỏ `SYSTEM` từ một mục tiêu hiện có. Bạn cũng có các tính năng gỡ lỗi mở rộng, và khả năng buộc tất cả các liên kết phải là các mục tiêu. Các preset có thể bao gồm các tệp khác. CUDA và C# nhận được các cập nhật mới, và một vài trình biên dịch đã được thêm vào.

- Phát hành lần đầu [29 tháng 3, 2022](https://blog.kitware.com/cmake-3-23-0-is-available-for-download/)
- Các preset của CMake trở nên tiện lợi hơn, với khả năng bao gồm các tệp khác.
- Một vài trình biên dịch mới được hỗ trợ, và hỗ trợ C# tốt hơn.
- `FILE_SET` cho `install` và `target_sources` các tệp nguồn chỉ có header.
- `<INTERFACE_>HEADER_SETS`, `<INTERFACE_>HEADER_DIRS` cho các header mục tiêu.
- Hỗ trợ `CUDA_ARCHITECTURES` cho tất cả và tất cả-major.
- Các thông điệp DEBUG có thể được bật cho `find_*` hoặc các module tìm kiếm.
- `define_property()` có tùy chọn tiện lợi `INITIALIZE_FROM_VARIABLE`.
- `CMAKE_<SYSTEM_>IGNORE_PREFIX_PATH` để kiểm soát các lệnh `find_*`.
- `<CMAKE_>LINK_LIBRARIES_ONLY_TARGETS` được thêm vào để buộc chỉ liên kết các mục tiêu (tốt để tìm lỗi!).
- `IMPORTED_NO_SYSTEM`, một thuộc tính mới để buộc loại bỏ SYSTEM từ một mục tiêu.
- `FindGTest` hiện thêm một mục tiêu `GMock` nếu tìm thấy.
## [CMake 3.22][]: Biến môi trường tiện dụng

Một phiên bản nhỏ hơn với một số cải tiến đáng chú ý tập trung vào việc hỗ trợ các tình huống build phổ biến. Bạn cuối cùng cũng có thể đặt `CMAKE_BUILD_TYPE` trong môi trường của mình để đặt loại build mặc định. Cũng có một số biến môi trường và biến mới khác. Các cờ trình biên dịch liên quan đến tiêu chuẩn đã được cải thiện. `cmake_host_system_information` được cải thiện thêm (từ 3.10) với thông tin hệ điều hành.

- Phát hành lần đầu [18 tháng 11, 2021](https://blog.kitware.com/cmake-3-22-0-available-for-download/)
- Các biến môi trường mới cho các giá trị mặc định, `CMAKE_BUILD_TYPE` và `CMAKE_CONFIGURATION_TYPES`
- Biến môi trường mới `CMAKE_INSTALL_MODE` cho các loại cài đặt (symlinks)
- Biến mới `CMAKE_REQUIRE_FIND_PACKAGE_<PackageName>` để chuyển một tìm kiếm tùy chọn thành bắt buộc
- `CMAKE_<LANG>_EXTENSIONS_DEFAULT` lấy từ trình biên dịch
- `CMakeDependentOption` hiện sử dụng cú pháp điều kiện thông thường
- CTest hiện có thể thay đổi các biến môi trường
- Một số generator hiện sử dụng các dấu hiệu bên ngoài (hệ thống) trên các include cho MSVC

## [CMake 3.21][]: Màu sắc

Các loại thông báo khác nhau hiện có màu sắc khác nhau! Hiện có một biến tiện lợi để xem liệu bạn có đang ở dự án cấp cao nhất hay không. Nhiều cải tiến và tính năng mới chuyên biệt tiếp tục được thêm vào, chẳng hạn như thêm ngôn ngữ HIP và hỗ trợ C17 và C23. Các preset tiếp tục được cải thiện.

- Phát hành lần đầu [14 tháng 7, 2021](https://blog.kitware.com/cmake-3-21-0-available-for-download/)
- Hỗ trợ sơ bộ cho MSVC 2022
- `CMAKE_<LANG>_LINKER_LAUNCHER` được thêm vào cho make và ninja
- HIP được thêm vào như một ngôn ngữ
- Hỗ trợ C17 và C23 được thêm vào
- `--install-prefix <dir>` và `--toolchain <file>` được thêm vào khi chạy CMake
- Các thông báo in ra được tô màu theo loại thông báo!
- Hỗ trợ cho MSYS, bao gồm `FindMsys`
- Lệnh `file(` nhận được một số cập nhật, bao gồm `EXPAND_TILDE`
- Hỗ trợ cho các phụ thuộc runtime và các artifact được thêm vào `install`
- `PROJECT_IS_TOP_LEVEL` và `<PROJECT-NAME>_IS_TOP_LEVEL` cuối cùng cũng được thêm vào
- Cải tiến bộ nhớ đệm cho các lệnh `find_`


[releases]: https://cmake.org/cmake/help/latest/release/index.html
[cmake 3.0]: https://cmake.org/cmake/help/latest/release/3.0.html
[cmake 3.1]: https://cmake.org/cmake/help/latest/release/3.1.html
[cmake 3.2]: https://cmake.org/cmake/help/latest/release/3.2.html
[cmake 3.3]: https://cmake.org/cmake/help/latest/release/3.3.html
[cmake 3.4]: https://cmake.org/cmake/help/latest/release/3.4.html
[cmake 3.5]: https://cmake.org/cmake/help/latest/release/3.5.html
[cmake 3.6]: https://cmake.org/cmake/help/latest/release/3.6.html
[cmake 3.7]: https://cmake.org/cmake/help/latest/release/3.7.html
[cmake 3.8]: https://cmake.org/cmake/help/latest/release/3.8.html
[cmake 3.9]: https://cmake.org/cmake/help/latest/release/3.9.html
[cmake 3.10]: https://cmake.org/cmake/help/latest/release/3.10.html
[cmake 3.11]: https://cmake.org/cmake/help/latest/release/3.11.html
[cmake 3.12]: https://cmake.org/cmake/help/latest/release/3.12.html
[cmake 3.13]: https://cmake.org/cmake/help/latest/release/3.13.html
[cmake 3.14]: https://cmake.org/cmake/help/latest/release/3.14.html
[cmake 3.15]: https://cmake.org/cmake/help/latest/release/3.15.html
[cmake 3.16]: https://cmake.org/cmake/help/latest/release/3.16.html
[cmake 3.17]: https://cmake.org/cmake/help/latest/release/3.17.html
[cmake 3.18]: https://cmake.org/cmake/help/latest/release/3.18.html
[cmake 3.19]: https://cmake.org/cmake/help/latest/release/3.19.html
[cmake 3.20]: https://cmake.org/cmake/help/latest/release/3.20.html
[cmake 3.21]: https://cmake.org/cmake/help/latest/release/3.21.html
[cmake 3.22]: https://cmake.org/cmake/help/latest/release/3.22.html
[cmake 3.23]: https://cmake.org/cmake/help/latest/release/3.23.html
[cmake 3.24]: https://cmake.org/cmake/help/latest/release/3.24.html
[cmake 3.25]: https://cmake.org/cmake/help/latest/release/3.25.html
[cmake 3.26]: https://cmake.org/cmake/help/latest/release/3.26.html
[cmake 3.27]: https://cmake.org/cmake/help/latest/release/3.27.html
[cmake 3.28]: https://cmake.org/cmake/help/latest/release/3.28.html
[cmake 3.29]: https://cmake.org/cmake/help/latest/release/3.29.html
[cmake 3.30]: https://cmake.org/cmake/help/latest/release/3.30.html
[cmake master]: https://cmake.org/cmake/help/git-master/release/index.html
[fastercmake]: https://blog.kitware.com/improving-cmakes-runtime-performance/
