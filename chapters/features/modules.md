# Các mô-đun hữu ích

Có rất nhiều mô-đun hữu ích trong bộ sưu tập `modules` của CMake; nhưng một số trong số chúng hữu ích hơn những cái khác. Dưới đây là một số điểm nổi bật.

## `CMakeDependentOption`

Mô-đun này thêm một lệnh `cmake_dependent_option` để thiết lập một tùy chọn dựa trên một tập hợp các biến khác là đúng. Nó trông như thế này:

```cmake
include(CMakeDependentOption)
cmake_dependent_option(BUILD_TESTS "Xây dựng các bài kiểm tra của bạn" ON "VAL1;VAL2" OFF)
```

mà chỉ là một cách viết tắt cho điều này:

```cmake
if(VAL1 AND VAL2)
    set(BUILD_TESTS_DEFAULT ON)
else()
    set(BUILD_TESTS_DEFAULT OFF)
endif()

option(BUILD_TESTS "Xây dựng các bài kiểm tra của bạn" ${BUILD_TESTS_DEFAULT})

if(NOT BUILD_TESTS_DEFAULT)
    mark_as_advanced(BUILD_TESTS)
endif()
```

Lưu ý rằng `BUILD_TESTING` là một cách tốt hơn để kiểm tra xem thử nghiệm có được bật hay không nếu bạn sử dụng `include(CTest)`, vì nó được định nghĩa sẵn cho bạn. Đây chỉ là một ví dụ về `CMakeDependentOption`.

## `CMakePrintHelpers`

Mô-đun này có một vài hàm xuất ra tiện dụng. `cmake_print_properties` cho phép bạn dễ dàng in các thuộc tính.
Và `cmake_print_variables` sẽ in tên và giá trị của bất kỳ biến nào bạn cung cấp cho nó.

## `CheckCXXCompilerFlag`

Mô-đun này kiểm tra xem một cờ (flag) có được hỗ trợ hay không. Ví dụ:

```cmake
include(CheckCXXCompilerFlag)
check_cxx_compiler_flag(-someflag OUTPUT_VARIABLE)
```

Lưu ý rằng `OUTPUT_VARIABLE` cũng sẽ xuất hiện trong bản in cấu hình, vì vậy hãy chọn một cái tên phù hợp.

Đây chỉ là một trong nhiều mô-đun tương tự, chẳng hạn như `CheckIncludeFileCXX`, `CheckStructHasMember`, `TestBigEndian` và `CheckTypeSize` cho phép bạn
kiểm tra thông tin về hệ thống (và bạn có thể truyền đạt thông tin đó đến mã nguồn của mình).

## `try_compile`/`try_run`

Đây không hẳn là một mô-đun, nhưng rất quan trọng đối với nhiều mô-đun được liệt kê ở trên. Bạn có thể thử biên dịch (và có thể chạy) một đoạn mã tại thời điểm cấu hình. Điều này có thể cho phép bạn nhận được thông tin về khả năng của hệ thống của mình. Cú pháp cơ bản là:

```cmake
try_compile(
  RESULT_VAR
    bindir
  SOURCES
    source.cpp
)
```

Có rất nhiều tùy chọn bạn có thể thêm, như `COMPILE_DEFINITIONS`. Trong CMake 3.8+, điều này sẽ tuân theo các cài đặt tiêu chuẩn CMake C/C++/CUDA. Nếu bạn sử dụng `try_run` thay vào đó, nó sẽ chạy chương trình kết quả và cung cấp cho bạn đầu ra trong `RUN_OUTPUT_VARIABLE`.

## `FeatureSummary`

Đây là một mô-đun khá hữu ích nhưng khá kỳ lạ. Nó cho phép bạn in ra một danh sách các gói đã được tìm kiếm, cũng như bất kỳ tùy chọn nào bạn đánh dấu rõ ràng. Nó được liên kết một phần nhưng không hoàn toàn với `find_package`. Trước tiên, bạn include mô-đun, như mọi khi:

```cmake
include(FeatureSummary)
```

Sau đó, đối với bất kỳ gói tìm kiếm nào bạn đã chạy hoặc sẽ chạy, bạn có thể mở rộng thông tin mặc định:

```cmake
set_package_properties(OpenMP PROPERTIES
    URL "http://www.openmp.org"
    DESCRIPTION "Chỉ thị trình biên dịch song song"
    PURPOSE "Đây là những gì nó làm trong gói của tôi")
```

Bạn cũng có thể đặt `TYPE` của một gói thành `RUNTIME`, `OPTIONAL`, `RECOMMENDED` hoặc `REQUIRED`; tuy nhiên, bạn không thể hạ thấp loại gói; nếu bạn đã thêm một gói `REQUIRED` thông qua `find_package` dựa trên một tùy chọn, bạn sẽ thấy nó được liệt kê là `REQUIRED`.

Và, bạn có thể đánh dấu bất kỳ tùy chọn nào là một phần của tóm tắt tính năng. Nếu bạn chọn cùng tên với một gói, hai gói sẽ tương tác với nhau.

```cmake
add_feature_info(WITH_OPENMP OpenMP_CXX_FOUND "OpenMP (Chỉ hàm FCN an toàn cho luồng)")
```

Sau đó, bạn có thể in ra tóm tắt các tính năng, lên màn hình hoặc tệp nhật ký:

```cmake
if(CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME)
    feature_summary(WHAT ENABLED_FEATURES DISABLED_FEATURES PACKAGES_FOUND)
    feature_summary(FILENAME ${CMAKE_CURRENT_BINARY_DIR}/features.log WHAT ALL)
endif()
```

Bạn có thể xây dựng bất kỳ bộ sưu tập các mục `WHAT` nào bạn thích hoặc chỉ sử dụng `ALL`.
