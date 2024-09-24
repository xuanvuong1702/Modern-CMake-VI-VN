## GoogleTest

GoogleTest và GoogleMock là những lựa chọn kinh điển; cá nhân tôi khuyên bạn nên dùng Catch2 thay thế, vì GoogleTest tuân theo triết lý phát triển của Google; nó loại bỏ các trình biên dịch cũ rất nhanh, nó giả định rằng người dùng muốn sống ở HEAD, v.v. Việc thêm GoogleMock cũng thường gây khó khăn - và bạn cần GoogleMock để có được các trình khớp (matcher), vốn là một tính năng mặc định trong Catch2 (nhưng không phải doctest).

### Phương pháp Submodule (ưu tiên)

Để sử dụng phương pháp này, chỉ cần checkout GoogleTest dưới dạng submodule: [^1]

```bash
git submodule add --branch=release-1.8.0 ../../google/googletest.git extern/googletest
```

Sau đó, trong `CMakeLists.txt` chính của bạn:

```cmake
option(PACKAGE_TESTS "Build các bài kiểm tra" ON)
if(PACKAGE_TESTS)
    enable_testing()
    include(GoogleTest)
    add_subdirectory(tests)
endif()
```

Tôi khuyên bạn nên sử dụng một cái gì đó giống như `PROJECT_NAME STREQUAL CMAKE_PROJECT_NAME` để đặt giá trị mặc định cho tùy chọn `PACKAGE_TESTS`, vì điều này sẽ chỉ build theo mặc định nếu đây là dự án hiện tại.
Như đã đề cập trước đây, bạn phải thực hiện `enable_testing` trong CMakeLists chính của mình.

Bây giờ, trong thư mục kiểm tra của bạn:

```cmake
add_subdirectory("${PROJECT_SOURCE_DIR}/extern/googletest" "extern/googletest")
```

Nếu bạn đã làm điều này trong CMakeLists chính của mình, bạn có thể sử dụng `add_subdirectory` bình thường; đường dẫn bổ sung ở đây là cần thiết để sửa đường dẫn build vì chúng ta đang gọi nó từ một thư mục con.

Dòng tiếp theo là tùy chọn, nhưng giúp `CACHE` của bạn sạch hơn:

```cmake
mark_as_advanced(
    BUILD_GMOCK BUILD_GTEST BUILD_SHARED_LIBS
    gmock_build_tests gtest_build_samples gtest_build_tests
    gtest_disable_pthreads gtest_force_shared_crt gtest_hide_internal_symbols
)
```

Nếu bạn quan tâm đến việc giữ cho các IDE hỗ trợ thư mục sạch sẽ, tôi cũng sẽ thêm các dòng sau:

```cmake
set_target_properties(gtest PROPERTIES FOLDER extern)
set_target_properties(gtest_main PROPERTIES FOLDER extern)
set_target_properties(gmock PROPERTIES FOLDER extern)
set_target_properties(gmock_main PROPERTIES FOLDER extern)
```

Sau đó, để thêm một bài kiểm tra, tôi khuyên bạn nên sử dụng macro sau:

```cmake
macro(package_add_test TESTNAME)
    # tạo một tệp thực thi trong đó các bài kiểm tra sẽ được lưu trữ
    add_executable(${TESTNAME} ${ARGN})
    # liên kết cơ sở hạ tầng kiểm tra Google, thư viện chế nhạo và hàm main mặc định vào
    # tệp thực thi kiểm tra. Xóa g_test_main nếu tự viết hàm main của riêng bạn.
    target_link_libraries(${TESTNAME} gtest gmock gtest_main)
    # gtest_discover_tests thay thế gtest_add_tests,
    # xem https://cmake.org/cmake/help/v3.10/module/GoogleTest.html để biết thêm các tùy chọn để chuyển cho nó
    gtest_discover_tests(${TESTNAME}
        # đặt thư mục làm việc để gốc dự án của bạn có thể tìm thấy dữ liệu kiểm tra thông qua các đường dẫn tương đối so với gốc dự án
        WORKING_DIRECTORY ${PROJECT_SOURCE_DIR}
        PROPERTIES VS_DEBUGGER_WORKING_DIRECTORY "${PROJECT_SOURCE_DIR}"
    )
    set_target_properties(${TESTNAME} PROPERTIES FOLDER tests)
endmacro()

package_add_test(test1 test1.cpp)
```

Điều này sẽ cho phép bạn thêm các bài kiểm tra một cách nhanh chóng và đơn giản. Hãy thoải mái điều chỉnh cho phù hợp với nhu cầu của bạn. Nếu bạn chưa từng thấy nó trước đây, `ARGN` là "mọi đối số sau những đối số được liệt kê".
Sửa đổi macro để đáp ứng nhu cầu của bạn. Ví dụ: nếu bạn đang kiểm tra các thư viện và cần liên kết các thư viện khác nhau cho các bài kiểm tra khác nhau, bạn có thể sử dụng điều này:

```cmake
macro(package_add_test_with_libraries TESTNAME FILES LIBRARIES TEST_WORKING_DIRECTORY)
    add_executable(${TESTNAME} ${FILES})
    target_link_libraries(${TESTNAME} gtest gmock gtest_main ${LIBRARIES})
    gtest_discover_tests(${TESTNAME}
        WORKING_DIRECTORY ${TEST_WORKING_DIRECTORY}
        PROPERTIES VS_DEBUGGER_WORKING_DIRECTORY "${TEST_WORKING_DIRECTORY}"
    )
    set_target_properties(${TESTNAME} PROPERTIES FOLDER tests)
endmacro()

package_add_test_with_libraries(test1 test1.cpp lib_to_test "${PROJECT_DIR}/european-test-data/")
```

### Phương pháp tải xuống

Bạn có thể sử dụng trình tải xuống trong [kho lưu trữ trợ giúp CMake][cliutils/cmake] của tôi, bằng cách sử dụng lệnh `include` của CMake.

Đây là trình tải xuống cho [GoogleTest], dựa trên công cụ [DownloadProject] tuyệt vời. Việc tải xuống một bản sao cho mỗi dự án là cách được khuyến nghị để sử dụng GoogleTest (trên thực tế, họ đã vô hiệu hóa target cài đặt CMake tự động), vì vậy điều này tôn trọng quyết định thiết kế đó. Phương pháp này tải xuống dự án tại thời điểm cấu hình, để IDE tìm thấy chính xác các thư viện. Sử dụng nó rất đơn giản:

```cmake
cmake_minimum_required(VERSION 3.15)
project(MyProject CXX)
list(APPEND CMAKE_MODULE_PATH ${PROJECT_SOURCE_DIR}/cmake)

enable_testing() # Phải có trong tệp chính

include(AddGoogleTest) # Có thể có trong /tests/CMakeLists.txt
add_executable(SimpleTest SimpleTest.cu)
add_gtest(SimpleTest)
```

> Lưu ý: `add_gtest` chỉ là một macro thêm `gtest`, `gmock` và `gtest_main`, sau đó chạy `add_test` để tạo một bài kiểm tra có cùng tên:
>
> ```cmake
> target_link_libraries(SimpleTest gtest gmock gtest_main)
> add_test(SimpleTest SimpleTest)
> ```

### FetchContent: CMake 3.11

Ví dụ cho mô-đun FetchContent là GoogleTest:

```cmake
include(FetchContent)

FetchContent_Declare(
  googletest
  GIT_REPOSITORY https://github.com/google/googletest.git
  GIT_TAG        release-1.8.0
)

FetchContent_GetProperties(googletest)
if(NOT googletest_POPULATED)
  FetchContent_Populate(googletest)
  add_subdirectory(${googletest_SOURCE_DIR} ${googletest_BINARY_DIR})
endif()
```

[^1]: Ở đây tôi đã giả định rằng bạn đang làm việc trên kho lưu trữ GitHub bằng cách sử dụng đường dẫn tương đối đến googletest.


[cliutils/cmake]: https://github.com/CLIUtils/cmake
[googletest]: https://github.com/google/googletest
[downloadproject]: https://github.com/Crascit/DownloadProject

**Giải thích:**

Tài liệu này hướng dẫn cách tích hợp GoogleTest (và GoogleMock) - một framework kiểm thử đơn vị phổ biến cho C++ - vào dự án CMake của bạn. 

**Các phương pháp được đề cập:**

1. **Sử dụng Git Submodule (khuyến nghị):**
   - Thêm GoogleTest vào dự án của bạn dưới dạng submodule Git.
   - Bật kiểm thử bằng `enable_testing()`.
   - Include mô-đun GoogleTest và thêm thư mục kiểm tra bằng `add_subdirectory`.
   - Sử dụng macro `package_add_test` (hoặc `package_add_test_with_libraries`) để thêm các bài kiểm tra một cách dễ dàng.

2. **Sử dụng phương pháp tải xuống:**
   - Sử dụng trình tải xuống từ kho lưu trữ CMake helper của tác giả để tải xuống GoogleTest trong quá trình cấu hình.
   - Sử dụng macro `add_gtest` để thêm bài kiểm tra và liên kết với các thư viện GoogleTest.

3. **Sử dụng FetchContent (CMake 3.11+):**
   - Sử dụng mô-đun `FetchContent` để tải xuống GoogleTest trong quá trình cấu hình.
   - Thêm GoogleTest vào dự án bằng `add_subdirectory`.

**Lưu ý:**

- Tác giả khuyến nghị sử dụng Catch2 thay vì GoogleTest vì lý do tương thích ngược và tính năng.
- GoogleTest khuyến nghị tải xuống một bản sao cho mỗi dự án, thay vì cài đặt toàn cục.
- `enable_testing()` phải được gọi trong tệp CMakeLists.txt chính.

**Tóm tắt:**

Tài liệu cung cấp hướng dẫn chi tiết về cách tích hợp GoogleTest vào dự án CMake của bạn bằng ba phương pháp khác nhau. Bạn có thể chọn phương pháp phù hợp nhất với nhu cầu của mình. 


