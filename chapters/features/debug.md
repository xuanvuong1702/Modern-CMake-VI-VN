# Gỡ lỗi mã

Bạn có thể cần gỡ lỗi bản dựng CMake của mình hoặc gỡ lỗi mã C++ của bạn. Cả hai đều được đề cập ở đây.

## Gỡ lỗi CMake

Đầu tiên, hãy xem xét các cách để gỡ lỗi CMakeLists hoặc tệp CMake khác.

### In biến

Phương pháp in câu lệnh đã được kiểm chứng theo thời gian trông như thế này trong CMake:

```cmake
message(STATUS "MY_VARIABLE=${MY_VARIABLE}")
```

Tuy nhiên, một mô-đun tích hợp sẵn giúp việc này dễ dàng hơn nữa:

```cmake
include(CMakePrintHelpers)
cmake_print_variables(MY_VARIABLE)
```

Nếu bạn muốn in ra một thuộc tính, điều này sẽ đẹp hơn rất nhiều! Thay vì lấy từng thuộc tính một trong số mỗi mục tiêu (hoặc mục khác có thuộc tính, chẳng hạn như `SOURCES`, `DIRECTORIES`, `TESTS` hoặc `CACHE_ENTRIES` - thuộc tính toàn cục dường như bị thiếu vì lý do nào đó), bạn có thể chỉ cần liệt kê chúng và in chúng trực tiếp:

```cmake
cmake_print_properties(
    TARGETS my_target
    PROPERTIES POSITION_INDEPENDENT_CODE
)
```

### Theo dõi quá trình chạy

Bạn đã bao giờ muốn xem chính xác những gì xảy ra trong tệp CMake của mình và khi nào chưa? Tính năng `--trace-source="filename"` rất tuyệt vời. Mọi dòng chạy trong tệp mà bạn cung cấp sẽ được lặp lại trên màn hình khi nó được chạy, cho phép bạn theo dõi chính xác những gì đang xảy ra. Cũng có các tùy chọn liên quan, nhưng chúng có xu hướng làm bạn ngập trong đầu ra.

Ví dụ:

```bash
cmake -S . -B build --trace-source=CMakeLists.txt
```

Nếu bạn thêm `--trace-expand`, các biến sẽ được mở rộng thành các giá trị của chúng.

## Xây dựng ở chế độ gỡ lỗi

Đối với các trình tạo cấu hình đơn, bạn có thể xây dựng mã của mình bằng `-DCMAKE_BUILD_TYPE=Debug` để nhận các cờ gỡ lỗi. Trong các trình tạo đa cấu hình, như nhiều IDE, bạn có thể chọn cấu hình trong IDE. Có các cờ riêng biệt cho chế độ này (các biến kết thúc bằng `_DEBUG` trái ngược với `_RELEASE`), cũng như giá trị biểu thức trình tạo `CONFIG:Debug` hoặc `CONFIG:Release`.

Sau khi bạn tạo bản dựng gỡ lỗi, bạn có thể chạy trình gỡ lỗi, chẳng hạn như gdb hoặc lldb trên đó.
