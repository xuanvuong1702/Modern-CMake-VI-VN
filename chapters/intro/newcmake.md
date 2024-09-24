# Có gì mới trong CMake

Đây là phiên bản rút gọn của nhật ký thay đổi CMake với những điểm nổi bật dành cho tác giả. Tên cho mỗi bản phát hành được tác giả chọn một cách tùy ý.

## [CMake đang phát triển][cmake master]: WIP

- LFortan hiện được hỗ trợ
- `$comment` được hỗ trợ trong các tệp preset
- `cmake -LR <regex>` để tìm kiếm trong bộ nhớ cache

## [CMake 3.30][]: C++26

Bản phát hành này bổ sung hỗ trợ C++26 và một cách để nhận tiêu chuẩn được hỗ trợ mới nhất
cho một trình biên dịch. Bản phát hành này tạo ra rất nhiều thay đổi nhỏ không được liệt kê bên dưới,
như hỗ trợ TLS tốt hơn, một số cập nhật biểu thức trình tạo và một số cập nhật lược đồ.

- Phát hành lần đầu vào [ngày 2 tháng 7 năm 2024](https://www.kitware.com/cmake-3-30-0-available-for-download/)
- Biểu thức trình tạo `$<QUOTE>` đã được thêm vào để tạo ra `"`
- Hỗ trợ các tính năng biên dịch C++26 được triển khai đầy đủ (một phần kể từ 3.25)
- `CMAKE_<LANG>_STANDARD_LATEST` chứa tiêu chuẩn mới nhất mà trình biên dịch hiện tại hỗ trợ
- Hỗ trợ Python 3.13+ đa luồng
- Hỗ trợ tốt hơn (các biến và mục tiêu mới) cho các bản dựng gỡ lỗi Python của Windows và `DEBUG_POSTFIX` hiện được thêm bởi `python_add_library`
- FindBoost đã bị xóa
- Hỗ trợ Visual Studio 2008 đã bị xóa

## [CMake 3.29][]: Xây dựng trước khi kiểm tra

Cuối cùng bạn có thể làm cho mục tiêu `test` phụ thuộc vào `ALL`, nghĩa là `cmake --build
build -t test` sẽ xây dựng lại khi cần thiết! Tuy nhiên, bạn phải chọn tham gia điều này bằng cách
đặt `CMAKE_SKIP_TEST_ALL_DEPENDENCY` thành false. Một số cải tiến đã được thực hiện
cho việc tạo kịch bản, lựa chọn trình liên kết và hỗ trợ đã được cải thiện cho các
kết hợp trình biên dịch khác nhau trên Windows.

- Phát hành lần đầu vào [ngày 21 tháng 3 năm 2024](https://www.kitware.com/cmake-3-29-0-available-for-download/)
- Tùy chọn lựa chọn trình liên kết (`CMAKE_LINKER_TYPE`/`LINKER_TYPE`)
- `CMAKE_INSTALL_PREFIX` hiện có thể được khởi tạo bởi một biến môi trường phù hợp
- Nếu các lệnh để kiểm tra quyền tệp
- Chọn trình khởi chạy cho các bài kiểm tra (`CMAKE_TEST_LAUNCHER`/`TEST_LAUNCHER`)
- Giờ đây, bạn có thể làm cho các bài kiểm tra phụ thuộc vào `all` với `CMAKE_SKIP_TEST_ALL_DEPENDENCY` được đặt thành `FALSE`!
- `cmake_language(EXIT)` cho các tập lệnh có mã thoát
- Chọn trình biên dịch Intel OneAPI Fortran với Visual Studio
- Biên dịch CUDA trên Windows bằng Clang

## [CMake 3.28][]: Mô-đun C++20

Bản phát hành này bổ sung hỗ trợ [mô-đun C++](https://cmake.org/cmake/help/latest/manual/cmake-cxxmodules.7.html).
Điều này không bao gồm `import std` của C++23, nhưng là bước tiến thú vị
cho tính năng quan trọng của C++20 này. Chúng sẽ được quét theo mặc định nếu
sử dụng C++20+ và trình biên dịch đủ mới và trình tạo hợp lệ và nếu bạn có
CMake tối thiểu hoặc tối đa bao gồm CMake 3.28.

- Phát hành lần đầu vào [ngày 6 tháng 12 năm 2023](https://www.kitware.com/cmake-3-28-0-available-for-download/)
- Các mô-đun được đặt tên C++20 được hỗ trợ bởi Ninja 1.11+ và MSVC 17.4+.
- `HIP` được hỗ trợ cho NVIDIA.
- VisionOS của Apple đã được thêm vào.
- Biến môi trường `CMAKE_CROSSCOMPILING_EMULATOR` đã được thêm vào.
- Hỗ trợ Get/set thuộc tính TEST cho các DIRECTORY khác
- Đã thêm một số hỗ trợ cho máy chủ công việc
- Hỗ trợ truyền biến cho pkg-config
- Các biểu thức trình tạo hiện hỗ trợ ngắn mạch

## [CMake 3.27][]: Trình gỡ lỗi

Bản phát hành này bổ sung trình gỡ lỗi CMake mới! Điều này sẽ cải thiện hỗ trợ cho
việc gỡ lỗi mã CMake của bạn trong một cái gì đó như VSCode. Bản phát hành này cũng
"loại bỏ" FindPythonLibs/FindPythonInterp/FindCUDA; nếu phiên bản tối thiểu hoặc tối đa
được đặt thành 3.27 trở lên, các mô-đun sẽ bị thiếu.

- Phát hành lần đầu vào [ngày 19 tháng 7 năm 2023](https://www.kitware.com/cmake-3-27-0-available-for-download/)
- Các phần mở rộng Mô-đun C++ (`.ccm`, `.cxxm`, `.c++m`) được coi là C++
- Các biểu thức trình tạo `COMPILE_ONLY`, `LIST` và `PATH` đã được thêm vào, cùng với một số biểu thức cụ thể hơn.
- `SKIP_LINTING` mới, cũng như hỗ trợ biểu thức trình tạo nhiều hơn trong những thứ như `<LANG>_CPPCHECK`, v.v.
- `find_package` hiện tìm kiếm các biến CMake/Environment `<PACKAGENAME>_ROOT` viết hoa.
- Đã thêm `add_custom_command(... DEPENDS_EXPLICIT_ONLY` & biến để kiểm soát phụ thuộc Ninja.
- CMake build verbose hiện in thư mục làm việc và dòng lệnh được sử dụng để xây dựng.
- Hỗ trợ tốt hơn cho các phiên bản của MSVC.
- Một số thuộc tính CUDA mới liên quan đến việc nhắm mục tiêu .`cubin`/`.fatbin`/`.optixir`.
- Việc đặt `cmake_minimum_required` nhỏ hơn 3.5 hiện không được dùng nữa.
- FindCUDA bán bị xóa, sử dụng ngôn ngữ CUDA và FindCUDAToolkit.
- FindPythonLibs & FindPythonInterp bán bị xóa, sử dụng FindPython.

## [CMake 3.26][]: Ghi nhật ký & Python

Hai bổ sung quan trọng cho FindPython, hỗ trợ PyPy SOABI & hỗ trợ LimitedAPI/StableABI, thực sự nâng cao khả năng sử dụng của FindPython. Có khá
nhiều bản sửa lỗi đẹp và cảnh báo mới, chẳng hạn như nếu bạn đảo ngược thứ tự của `project()` và `cmake_minimium_required()`. Ghi nhật ký đã được chuyển
từ `CMakeOutput.log` và `CMakeError.log` sang nhật ký `CMakeConfigureLog.yaml` mới.

- Phát hành lần đầu vào [ngày 14 tháng 3 năm 2023](https://www.kitware.com/cmake-3-26-0-available-for-download/)
- FindPython tạo ra PyPy SOABI chính xác (cuối cùng!)
- FindPython hỗ trợ LimitedAPI/StableABI với cờ mới.
- CMake có nhật ký YAML mới về các kiểm tra thời gian cấu hình trong thư mục đầu ra (cũng là `message(CONFIGURE_LOG ...)`).
- Ngôn ngữ `ASM_MARMASM` được thêm vào cho trình biên dịch ARM của Microsoft.
- `CMAKE_VS_VERSION_BUILD_NUMBER` được thêm vào cho số phiên bản VS.
- `USE_FOLDERS` được bật theo mặc định
- `"<LANG>_CLANG_TIDY_EXPORT_FIXES_DIR"` cho đầu ra sửa lỗi được đề xuất của clang-tidy.
- Công cụ CLI sao chép của CMake chỉ hỗ trợ cập nhật nếu khác
- `target_compile_options` hiện nằm sau `target_compile_features` / `CMAKE_<LANG>_STANDARD`

## [CMake 3.25][]: Khối và SYSTEM

CMake có các lệnh phân định phạm vi khối mới để kiểm soát có chọn lọc các biến và
chính sách. Nó cũng có nhiều quyền kiểm soát hơn đối với SYSTEM. Các tính năng chức năng
của CMake được giới thiệu một vài bản phát hành trước đây hiện có thể sử dụng được trong các lệnh `find_` với
`VALIDATOR`. Quy trình làm việc cũng được nâng cấp.

- Phát hành lần đầu vào [ngày 16 tháng 11 năm 2022](https://www.kitware.com/cmake-3-25-0-available-for-download/)
- Hỗ trợ C++26
- LTO cho CUDA với nvcc
- Đã thêm các cài đặt trước quy trình làm việc, cài đặt trước gói.
- `SYSTEM` được thêm vào `add_subdirectory`, `FetchContent` và như một thuộc tính thư mục.
- `block()`/`endblock()` cho phạm vi chính sách/biến, cũng như `PROPOGATE` trong `return()`
- Đã thêm các biến `BSD` & `LINUX`
- Hàm `VALIDATOR` cho các lệnh `find_*`.
- Một số cải tiến cho các lệnh `try_*`.
- Thuộc tính mục tiêu/thư mục `SYSTEM` và `EXPORT_NO_SYSTEM` đã được thêm vào, cũng dành cho FetchContent.

## [CMake 3.24][]: Trình tìm gói

Đây là một bản phát hành tuyệt vời. Những người viết gói đang nhận được sự tích hợp giữa
`find_package` và `FetchContent` sẽ cho phép quy trình làm việc "tải xuống nếu thiếu",
và có thể được cấu hình bởi những người đóng gói. Tương tự, cảnh báo dưới dạng lỗi có thể
được đặt bởi gói và cũng bị xóa bởi những người đóng gói (vẫn đảm bảo không
làm điều này trừ khi bạn đang được xây dựng là dự án chính!).

- Phát hành lần đầu vào [ngày 4 tháng 8 năm 2022](https://blog.kitware.com/cmake-3-24-0-is-available-for-download/)
- Tùy chọn `--fresh` loại bỏ bộ nhớ cache cũ khi chạy.
- `find_package` và `FetchContent` hiện có tích hợp - bạn có tùy chọn để tải xuống các phụ thuộc bị thiếu.
- `find_package` có tùy chọn `GLOBAL` mới.
- `CMAKE_PROJECT_TOP_LEVEL_INCLUDES` cho phép người dùng (như người đóng gói) chèn mã trước dự án.
- Quản lý `PATH` cho các biểu thức trình tạo.
- Biến môi trường & biến `CMAKE_COLOR_DIAGNOSTICS` đã được thêm vào, thay thế `CMAKE_COLOR_MAKEFILE`.
- Bạn có thể tắt `find_*` tìm kiếm tiền tố cài đặt.
- Thuộc tính `COMPILE_WARNING_AS_ERROR` và biến `CMAKE_`, và `--compile-no-warning-as-error` để tắt nó.
- CUDA hỗ trợ `native` để biên dịch cho GPU hiện tại được phát hiện.
- Bao gồm `SYSTEM` hiện được tôn trọng trên các trình tạo MSVC.
- Hỗ trợ tốt hơn cho MSVC, XCode và những thứ khác.
- Hỗ trợ trình biên dịch `LLVMFlang`.

## [CMake 3.23][]: Thư viện chỉ tiêu đề

Một bản phát hành vững chắc tập trung vào các thư viện chỉ tiêu đề, kiểm soát người dùng nhiều hơn,
cài đặt trước CMake và hỗ trợ CUDA tốt hơn. Có một số tính năng mới mạnh mẽ cho
các thư viện chỉ tiêu đề, chẳng hạn như các thuộc tính mục tiêu `*_SETS` khác nhau. Có
các điều khiển mới như khả năng hạn chế đường dẫn cho các lệnh `find_` và
khả năng xóa `SYSTEM` khỏi mục tiêu hiện có. Bạn cũng nhận được mở rộng
các tính năng gỡ lỗi và khả năng buộc tất cả các liên kết phải đến các mục tiêu.
Cài đặt trước có thể bao gồm các tệp khác. CUDA và C# đã nhận được các bản cập nhật mới và
một số trình biên dịch đã được thêm vào.

- Phát hành lần đầu vào [ngày 29 tháng 3 năm 2022](https://blog.kitware.com/cmake-3-23-0-is-available-for-download/)
- Cài đặt trước CMake tốt hơn một chút, với khả năng bao gồm các tệp khác.
- Một số trình biên dịch được hỗ trợ mới và hỗ trợ C# tốt hơn.
- `FILE_SET` cho `install` và `target_sources` các tệp nguồn chỉ tiêu đề.
- `<INTERFACE_>HEADER_SETS`, `<INTERFACE_>HEADER_DIRS` cho các tiêu đề mục tiêu.
- Hỗ trợ `CUDA_ARCHITECTURES` cho tất cả và all-major.
- Các thông báo DEBUG từ có thể được bật cho `find_*` hoặc các mô-đun tìm kiếm.
- `define_property()` có tùy chọn `INITIALIZE_FROM_VARIABLE` tiện dụng.
- `CMAKE_<SYSTEM_>IGNORE_PREFIX_PATH` để kiểm soát các lệnh `find_*`.
- `<CMAKE_>LINK_LIBRARIES_ONLY_TARGETS` được thêm vào để buộc chỉ các mục tiêu được liên kết
  (rất tốt để tìm lỗi!).
- `IMPORTED_NO_SYSTEM`, một thuộc tính mới để xóa SYSTEM khỏi mục tiêu một cách cưỡng bức.
- `FindGTest` hiện thêm mục tiêu `GMock` nếu được tìm thấy.

## [CMake 3.22][]: Biến môi trường tiện dụng

Một bản phát hành nhỏ hơn với một số cải tiến đẹp mắt xung quanh tập trung vào việc hỗ trợ
các tình huống bản dựng phổ biến. Cuối cùng, bạn có thể đặt `CMAKE_BUILD_TYPE` trong
môi trường của mình để đặt kiểu bản dựng mặc định. Ngoài ra còn có một số biến môi trường
và biến mới khác. Các cờ trình biên dịch liên quan đến tiêu chuẩn đã được cải thiện.
`cmake_host_system_information` đã được cải thiện thêm (từ 3.10) với thông tin hệ điều hành.

- Phát hành lần đầu vào [ngày 18 tháng 11 năm 2021](https://blog.kitware.com/cmake-3-22-0-available-for-download/)
- Các biến môi trường mới cho mặc định, `CMAKE_BUILD_TYPE` và `CMAKE_CONFIGURATION_TYPES`
- Biến môi trường mới `CMAKE_INSTALL_MODE` cho các loại cài đặt (liên kết tượng trưng)
- Biến `CMAKE_REQUIRE_FIND_PACKAGE_<PackageName>` mới để chuyển đổi tìm kiếm tùy chọn thành tìm kiếm bắt buộc
- `CMAKE_<LANG>_EXTENSIONS_DEFAULT` đến từ trình biên dịch
- `CMakeDependentOption` hiện sử dụng cú pháp điều kiện thông thường
- CTest hiện có thể sửa đổi các biến môi trường
- Một số trình tạo hiện sử dụng các dấu hiệu bên ngoài (hệ thống) trên includes cho MSVC

## [CMake 3.21][]: Màu sắc

Các loại thông báo khác nhau hiện có màu sắc khác nhau! Bây giờ có một biến đẹp
để xem liệu bạn có đang ở trong dự án cấp cao nhất hay không. Rất nhiều việc dọn dẹp tiếp tục và
các tính năng mới chuyên biệt, chẳng hạn như thêm ngôn ngữ HIP và hỗ trợ C17 và C23.
Cài đặt trước tiếp tục được cải thiện.

- Phát hành lần đầu vào [ngày 14 tháng 7 năm 2021](https://blog.kitware.com/cmake-3-21-0-available-for-download/)
- Hỗ trợ sơ bộ cho MSVC 2022
- `CMAKE_<LANG>_LINKER_LAUNCHER` được thêm vào cho make và ninja
- HIP được thêm vào như một ngôn ngữ
- Hỗ trợ C17 và C23 đã được thêm vào
- `--install-prefix <dir>` và `--toolchain <file>` được thêm vào khi chạy CMake
- Các thông báo được in có màu theo loại thông báo!
- Hỗ trợ cho MSYS, bao gồm `FindMsys`
- Lệnh `file(` đã nhận được một số bản cập nhật, bao gồm `EXPAND_TILDE`
- Hỗ trợ cho các phụ thuộc thời gian chạy và tạo tác được thêm vào `install`
- `PROJECT_IS_TOP_LEVEL` và `<PROJECT-NAME>_IS_TOP_LEVEL` cuối cùng đã được thêm vào
- Cải thiện bộ nhớ đệm cho các lệnh `find_`

## [CMake 3.20][]: Tài liệu

Tài liệu CMake đã nhận được sự tăng cường lớn về năng suất bằng cách thêm thẻ "mới trong"
để nhanh chóng xem những gì đã được thêm vào mà không cần phải chuyển đổi các phiên bản tài liệu!
Đã thêm hỗ trợ C++ 23. Các tệp nguồn hiện phải có phần mở rộng được liệt kê và
LANGUAGE luôn được tôn trọng. Khá nhiều việc dọn dẹp đã được thực hiện; hãy đảm bảo mã của bạn
được kiểm tra với `...3.20` trước khi triển khai nó làm mức tối đa của bạn.
Cài đặt trước tiếp tục được cải thiện.

- Phát hành lần đầu vào [ngày 23 tháng 3 năm 2021](https://blog.kitware.com/cmake-3-20-0-available-for-download/)
- Hỗ trợ được thêm vào cho C++23
- Biến môi trường CUDAARCHS để đặt kiến trúc CUDA
- Các trình biên dịch `IntelLLVM` mới hiện được hỗ trợ (OneAPI 2021.1) và SDK NVIDIA HPC `NVHPC` cũng vậy
- Một số hỗ trợ biểu thức trình tạo mở rộng trong các lệnh/mục tiêu tùy chỉnh, đổi tên cài đặt
- Lệnh `cmake_path` mới để làm việc với đường dẫn
- `try_run` hiện có `WORKING_DIRECTORY`
- Thêm tính năng cho lệnh `file(GENERATE`
- Một số loại bỏ, như `cmake-server`, `WriteCompilerDetectionHeader` (nếu chính sách được đặt thành 3.20+) và một số thứ hiện có phương pháp mới hơn.
- Các tệp nguồn phải bao gồm phần mở rộng

## [CMake 3.19][]: Cài đặt trước

Giờ đây, bạn có thể thêm cài đặt trước ở dạng JSON và người dùng sẽ nhận được cài đặt trước mặc định.
`find_package` hiện có thể lấy một phạm vi phiên bản và một số mô-đun tìm kiếm đặc biệt,
như FindPython, có hỗ trợ tùy chỉnh cho nó. Rất nhiều điều khiển mới đã được thêm vào
cho quyền. Hỗ trợ thêm cho các biểu thức trình tạo ở nhiều nơi hơn.

- Phát hành lần đầu vào [ngày 18 tháng 11 năm 2020](https://blog.kitware.com/cmake-3-19-0-available-for-download/)
- Các [tệp cài đặt trước CMake](https://cmake.org/cmake/help/latest/manual/cmake-presets.7.html) mới hiện được hỗ trợ - bạn có thể đặt mặc định cho dự án của mình cho mỗi trình tạo hoặc bạn có thể tạo cài đặt trước của Người dùng. PSA: Vui lòng thêm `CMakeUserPresets.json` vào `.gitignore` của bạn, ngay cả khi bạn không sử dụng `CMakePresets.json`.
- CMake hiện sử dụng hệ thống bản dựng mới được giới thiệu trong XCode 12+
- MSVC cho Android hiện được hỗ trợ
- `cmake -E create_hardlink` đã được thêm vào
- `add_test` cuối cùng cũng hỗ trợ đúng cách khoảng trắng trong tên kiểm tra
- Giờ đây, bạn có thể `DEFER` `cmake_language` để chạy vào cuối quá trình xử lý thư mục
- Rất nhiều tùy chọn `file` mới, như tải xuống tạm thời và `COMPRESSION_LEVEL` cho `ARCHIVE_CREATE`
- `find_package` hỗ trợ một phạm vi phiên bản
- `DIRECTORY` hiện có thể bao gồm một thư mục nhị phân trong các lệnh thuộc tính
- Các lệnh `JSON` mới cho `string`
- Thuộc tính `OPTIMIZE_DEPENDENCIES` mới và biến `CMAKE_*` để loại bỏ thông minh các phụ thuộc của các thư viện tĩnh và đối tượng.
- Hỗ trợ PCH được mở rộng với thuộc tính `PCH_INSTANTIATE_TEMPLATES` và biến `CMAKE_*`.
- Các mô-đun kiểm tra đã được mở rộng với các ngôn ngữ `CUDA` và `ISPC`
- FindPython: `Python*_LINK_OPTIONS` được thêm vào
- `compute-sanitizer` cho ctest hiện hỗ trợ CUDA cho memcheck

## [CMake 3.18][]: CUDA với Clang & Ngôn ngữ macro CMake

CUDA hiện hỗ trợ Clang (không có biên dịch tách biệt). Một `CUDA_ARCHITECTURES` mới
thuộc tính đã được triển khai để hỗ trợ tốt hơn việc nhắm mục tiêu phần cứng CUDA.
Một lệnh `cmake_language` mới hỗ trợ gọi các lệnh cmake và
biểu thức từ chuỗi. Rất nhiều thay đổi meta khác có thể tạo ra
thiết kế mới có sẵn; gọi các hàm theo biến, đánh giá CMake tùy ý bằng
chuỗi và cấu hình các tệp trực tiếp từ chuỗi. Nhiều tính năng nhỏ đẹp khác
và các bản sửa lỗi giấy cắt được rắc khắp nơi, một lựa chọn nhỏ ở bên dưới.

- Phát hành lần đầu vào [ngày 15 tháng 7 năm 2020](https://blog.kitware.com/cmake-3-18-0-available-for-download/)
- `cmake` có thể `cat` các tệp với nhau ngay bây giờ
- Chế độ cấu hình mới cho `cmake`
- `cmake_language` với `CALL` và `EVAL`
- `export` yêu cầu `APPEND` nếu được sử dụng nhiều lần (trong cấp độ ngôn ngữ CMake 3.18+)
- Bạn có thể lưu trữ trực tiếp từ `file()`
- `file(CONFIGURE` là một dạng đẹp hơn của `configure_file` nếu bạn đã có một chuỗi để tạo
- Các lệnh `find_*` khác nhận được cờ `REQUIRED` của `find_package`
- Sắp xếp `NATURAL` trong `list(SORT` đã được thêm vào
- Thêm tùy chọn để xử lý các thuộc tính với phạm vi DIRECTORY
- `CUDA_ARCHITECTURES` đã được thêm vào
- `LINK_LANGUAGE` mới biểu thức trình tạo (phiên bản `DEVICE`/`HOST` cũng vậy)
- Nguồn có thể là một thư mục con cho `FetchContent`

## [CMake 3.17][]: Thêm CUDA

Cuối cùng, FindCUDAToolkit đã được thêm vào, cho phép tìm và sử dụng CUDA
bộ công cụ mà không cần bật ngôn ngữ CUDA! CUDA hiện có thể cấu hình nhiều hơn một chút,
chẳng hạn như liên kết đến các thư viện dùng chung. Cũng có nhiều cải tiến hơn trong các lĩnh vực mong đợi,
như FindPython. Cuối cùng, giờ đây bạn có thể lặp lại nhiều danh sách cùng một lúc.

- Phát hành lần đầu vào [ngày 20 tháng 3 năm 2020](https://blog.kitware.com/cmake-3-17-0-available-for-download/)
- `CUDA_RUNTIME_LIBRARY` cuối cùng có thể được đặt thành Shared!
- FindCUDAToolkit cuối cùng đã được thêm vào
- `cmake -E rm` thay thế các lệnh xóa cũ hơn
- CUDA có các tính năng meta như `cuda_std_03`, v.v.
- Bạn có thể theo dõi việc tìm kiếm các gói bằng `--debug-find`
- ExternalProject hiện có thể tắt kiểm tra đệ quy
- FindPython tích hợp tốt hơn với Conda
- DEPRECATION có thể được áp dụng cho các mục tiêu
- CMake đã có một lệnh rm
- Một số biến môi trường mới
- foreach hiện có thể thực hiện `ZIP_LISTS` (nhiều danh sách cùng một lúc)

## [CMake 3.16][]: Bản dựng Unity

Một chế độ bản dựng unity mới đã được thêm vào, cho phép hợp nhất các tệp nguồn thành một tệp bản dựng duy nhất. Hỗ trợ cho
tiêu đề được biên dịch trước (có thể chuẩn bị cho các mô-đun C++20, có lẽ?) đã được thêm vào. Rất nhiều thứ nhỏ hơn khác
bản sửa lỗi đã được triển khai, đặc biệt là các tính năng mới hơn, chẳng hạn như FindPython, FindDoxygen và những tính năng khác.

- Phát hành lần đầu vào [ngày 26 tháng 11 năm 2019](https://blog.kitware.com/cmake-3-16-0-available-for-download/)
- Đã thêm hỗ trợ cho ngôn ngữ Objective C và Objective C++
- Hỗ trợ cho tiêu đề được biên dịch trước, với `target_precompile_headers`
- Hỗ trợ cho các bản dựng "Unity" hoặc "Jumbo" (hợp nhất các tệp nguồn) với {{ variable.format('CMAKE_UNITY_BUILD') }}
- CTest: Giờ đây có thể bỏ qua dựa trên biểu thức chính quy, mở rộng danh sách
- Một số tính năng mới để kiểm soát RPath.
- Các biểu thức trình tạo hoạt động ở nhiều nơi hơn, như đường dẫn bản dựng và cài đặt
- Vị trí tìm kiếm hiện có thể được kiểm soát rõ ràng thông qua các biến mới

## [CMake 3.15][]: Nâng cấp CLI

Bản phát hành này có nhiều thay đổi đánh bóng nhỏ hơn, bao gồm một số cải tiến cho dòng lệnh CMake, chẳng hạn như kiểm soát trình tạo mặc định thông qua các biến môi trường (vì vậy giờ đây việc thay đổi trình tạo mặc định thành Ninja rất dễ dàng). Nhiều mục tiêu được hỗ trợ ở chế độ `--build` và chế độ `--install` được thêm vào. CMake cuối cùng đã hỗ trợ nhiều cấp độ ghi nhật ký. Các biểu thức trình tạo đã đạt được một số công cụ tiện dụng. Mô-đun FindPython vẫn còn rất mới tiếp tục được cải thiện và FindBoost hiện phù hợp hơn với mô-đun CONFIG mới của Boost 1.70. `export(PACKAGE)` đã thay đổi đáng kể; giờ đây, nó không còn chạm vào `$HOME/.cmake` theo mặc định (nếu phiên bản CMake tối thiểu là 3.15 trở lên) và yêu cầu một bước bổ sung nếu người dùng muốn sử dụng nó. Điều này nhìn chung ít gây ngạc nhiên hơn.

- Phát hành lần đầu vào [ngày 17 tháng 7 năm 2019](https://blog.kitware.com/cmake-3-15-0-available-for-download/)
- Biến môi trường {{ envvar.format('CMAKE_GENERATOR') }} được thêm vào để kiểm soát trình tạo mặc định
- Hỗ trợ nhiều mục tiêu ở chế độ bản dựng, `cmake . --build --target a b`
- Phím tắt `-t` cho `--target`
- Hỗ trợ cài đặt, `cmake . --install`, không gọi hệ thống bản dựng
- Hỗ trợ cho `--loglevel` và `NOTICE`, `VERBOSE`, `DEBUG` và `TRACE` cho `message`
- Lệnh {{ command.format('list') }} đã đạt được `PREPEND`, `POP_FRONT` và `POP_BACK`
- {{ command.format('execute_process') }} đã đạt được tùy chọn `COMMAND_ECHO` ({{ variable.format('CMAKE_EXECUTE_PROCESS_COMMAND_ECHO') }}) cho phép bạn tự động lặp lại các lệnh trước khi chạy chúng
- Một số cải tiến của Ninja, bao gồm hỗ trợ ngôn ngữ SWIFT
- Các cải tiến về trình biên dịch và danh sách cho các biểu thức trình tạo

## [CMake 3.14][]: Các tiện ích tệp (AKA [CMake π](https://blog.kitware.com/kitware-gets-mathematical-with-cmake-π-on-pi-day/))

Bản phát hành này có rất nhiều bản dọn dẹp nhỏ, bao gồm một số tiện ích cho các tệp. Các biểu thức trình tạo hoạt động ở một số nơi hơn và xử lý danh sách tốt hơn với các biến trống.
Khá nhiều gói tìm kiếm hơn tạo ra các mục tiêu. Trình tạo Visual Studio 16 2019 mới hơi khác so với các phiên bản cũ hơn. Hỗ trợ Windows XP và Vista đã bị loại bỏ.

- Phát hành lần đầu vào [ngày 14 tháng 3 năm 2019](https://blog.kitware.com/cmake-3-14-0-available-for-download/)
- Lệnh cmake `--build` đã đạt được `-v/--verbose`, để sử dụng các bản dựng dài dòng nếu công cụ bản dựng của bạn hỗ trợ nó
- Lệnh FILE đã đạt được `CREATE_LINK`, `READ_SYMLINK` và `SIZE`
- {{ command.format('get_filename_component') }} đã đạt được `LAST_EXT` và `NAME_WLE` để chỉ truy cập phần mở rộng _cuối cùng_ trên tệp, điều này sẽ nhận được `.zip` trên tệp như `version.1.2.zip` (rất tiện dụng!)
- Bạn có thể xem liệu một biến có được xác định trong CACHE với `DEFINED CACHE{VAR}` trong câu lệnh {{ command.format('if') }} hay không.
- `BUILD_RPATH_USE_ORIGIN` và phiên bản CMake đã được thêm vào để cải thiện việc xử lý RPath trong thư mục bản dựng.
- Chế độ máy chủ CMake hiện đang được thay thế bằng API tệp, bắt đầu từ bản phát hành này. Sẽ ảnh hưởng đến IDE trong thời gian dài.

## [CMake 3.13][]: Kiểm soát liên kết

Giờ đây, bạn có thể tạo liên kết tượng trưng trên Windows! Rất nhiều chức năng mới bổ sung cho
các yêu cầu phổ biến cho CMake, chẳng hạn như `add_link_options`, `target_link_directories` và
`target_link_options`. Giờ đây, bạn có thể sửa đổi các mục tiêu nhiều hơn một chút
bên ngoài thư mục nguồn để tách tệp tốt hơn. Và, `target_sources` _cuối cùng_ xử lý các đường dẫn tương đối một cách chính xác (chính sách 76).

- Phát hành lần đầu vào [ngày 20 tháng 11 năm 2018](https://blog.kitware.com/cmake-3-13-0-available-for-download/)
- Tùy chọn `ctest --progress` mới cho đầu ra trực tiếp
- `target_link_options` và `add_link_options` được thêm vào
- `target_link_directories` được thêm vào
- Tạo liên kết tượng trưng, `-E create_symlink`, được hỗ trợ trên Windows
- IPO được hỗ trợ trên Windows
- Bạn có thể sử dụng `-S` và `-B` cho các thư mục nguồn và bản dựng
- `target_link_libraries` và `install` hoạt động bên ngoài thư mục mục tiêu hiện tại
- Thuộc tính `STATIC_LIBRARY_OPTIONS` được thêm vào
- `target_sources` hiện là tương đối so với thư mục nguồn hiện tại (CMP0076)
- Nếu bạn sử dụng Xcode, giờ đây bạn có thể đặt thử nghiệm các trường lược đồ

## [CMake 3.12][]: Phạm vi phiên bản và CONFIGURE_DEPENDS

Bản phát hành rất mạnh mẽ, chứa rất nhiều tính năng nhỏ được yêu cầu từ lâu. Một trong những
thay đổi nhỏ hơn nhưng đáng chú ý ngay lập tức là việc bổ sung phạm vi phiên bản;
giờ đây, bạn có thể dễ dàng đặt cả phiên bản CMake tối thiểu và tối đa đã biết. Bạn cũng có thể đặt
`CONFIGURE_DEPENDS` trên một tập hợp các tệp `GLOB` và hệ thống bản dựng sẽ kiểm tra các tệp đó và
chạy lại nếu cần! Bạn có thể sử dụng `PackageName_ROOT` chung
cho tất cả các tìm kiếm `find_package`. Rất nhiều bổ sung cho chuỗi và danh sách, cập nhật mô-đun,
mô-đun tìm kiếm Python mới sáng bóng (cả phiên bản 2 và 3) và nhiều hơn nữa.

- Phát hành lần đầu vào [ngày 17 tháng 7 năm 2018](https://blog.kitware.com/cmake-3-12-0-available-for-download/)
- Hỗ trợ cho phạm vi `cmake_minimum_required` (tương thích ngược)
- Hỗ trợ cho `-j,--parallel` ở chế độ `--build` (được chuyển đến công cụ bản dựng)
- Hỗ trợ cho chuỗi `SHELL:` trong các tùy chọn biên dịch (không được loại bỏ trùng lặp)
- Mô-đun FindPython mới
- `string(JOIN` và `list(JOIN`, và `list(TRANSFORM`
- `file(TOUCH` và `file(GLOB CONFIGURE_DEPENDS`
- Hỗ trợ C++20
- Cải tiến CUDA như một ngôn ngữ: CUDA 7 và 7.5 hiện được hỗ trợ
- Hỗ trợ cho OpenMP trên macOS (chỉ dòng lệnh)
- Một số thuộc tính và trình khởi tạo thuộc tính mới
- CPack cuối cùng đã đọc các biến `CMAKE_PROJECT_VERSION`

## [CMake 3.11][]: Nhanh hơn & GIAO DIỆN IMPORTED

Bản phát hành này [được cho là][fastercmake] nhanh hơn nhiều. Cuối cùng bạn cũng có thể thêm trực tiếp các mục tiêu INTERFACE
vào các thư viện IMPORTED (các tập lệnh `Find*.cmake` nội bộ cuối cùng sẽ trở nên sạch hơn nhiều).

- Phát hành lần đầu vào [ngày 28 tháng 3 năm 2018](https://blog.kitware.com/cmake-3-11-0-available-for-download/)
- Fortran hỗ trợ trình khởi chạy trình biên dịch
- Xcode và Visual Studio cuối cùng cũng hỗ trợ các biểu thức trình tạo `COMPILE_LANGUAGE`
- Giờ đây, bạn có thể thêm các mục tiêu INTERFACE trực tiếp vào các thư viện INTERFACE IMPORTED (Tuyệt vời!)
- Các thuộc tính tệp nguồn đã được mở rộng
- Mô-đun `FetchContent` hiện cho phép tải xuống diễn ra tại thời điểm cấu hình (Tuyệt vời)

## [CMake 3.10][]: CppCheck

CMake hiện được xây dựng bằng các trình biên dịch C++11. Rất nhiều cải tiến hữu ích giúp viết mã sạch hơn.

- Phát hành lần đầu vào [ngày 20 tháng 11 năm 2017](https://blog.kitware.com/cmake-3-10-0-available-for-download/)
- Hỗ trợ cho trình biên dịch flang Fortran
- Trình khởi chạy trình biên dịch được thêm vào CUDA
- `#cmakedefines` thụt lề hiện được hỗ trợ cho `configure_file`
- `include_guard()` được thêm vào để đảm bảo một tệp chỉ được bao gồm một lần
- `string(PREPEND` được thêm vào
- Thuộc tính `*_CPPCHECK` được thêm vào
- `LABELS` được thêm vào các thư mục
- FindMPI được mở rộng rất nhiều
- FindOpenMP được cải thiện
- Khám phá kiểm tra động cho `GoogleTest`
- `cmake_host_system_information` có thể truy cập nhiều thông tin hơn.

## [CMake 3.9][]: IPO

Rất nhiều bản sửa lỗi cho hỗ trợ CUDA đã được đưa vào bản phát hành này, bao gồm hỗ trợ `PTX` và các trình tạo MSVC. Tối ưu hóa liên thủ tục hiện được hỗ trợ đúng cách.
Càng nhiều mô-đun cung cấp các mục tiêu được nhập, bao gồm cả MPI.

- Phát hành lần đầu vào [ngày 18 tháng 7 năm 2017](https://blog.kitware.com/cmake-3-9