# Cách cấu trúc dự án của bạn

Những thông tin sau đây mang tính chủ quan. Nhưng theo một cách tích cực, tôi nghĩ vậy. Tôi sẽ hướng dẫn bạn cách cấu trúc các thư mục trong dự án của bạn. Cách này dựa trên quy ước, nhưng sẽ giúp bạn:

- Dễ dàng đọc các dự án khác tuân theo cùng một mẫu,
- Tránh các mẫu gây ra xung đột,
- Tránh làm rối và phức tạp quá trình build của bạn.

Đầu tiên, đây là những gì tệp của bạn sẽ trông như thế nào khi bạn bắt đầu nếu dự án của bạn được đặt tên một cách sáng tạo là `project`, với một thư viện có tên là `lib` và một tệp thực thi có tên là `app`:

```
- project
  - .gitignore
  - README.md
  - LICENSE.md
  - CMakeLists.txt
  - cmake
    - FindSomeLib.cmake
    - something_else.cmake
  - include
    - project
      - lib.hpp
  - src
    - CMakeLists.txt
    - lib.cpp
  - apps
    - CMakeLists.txt
    - app.cpp
  - tests
    - CMakeLists.txt
    - testlib.cpp
  - docs
    - CMakeLists.txt
  - extern
    - googletest
  - scripts
    - helper.py
```

Các tên không phải là tuyệt đối; bạn sẽ thấy tranh luận về `test/` so với `tests/`, và thư mục ứng dụng có thể được gọi bằng một tên khác (hoặc không tồn tại đối với dự án chỉ có thư viện). Đôi khi bạn cũng sẽ thấy một thư mục `python` cho các ràng buộc python hoặc một thư mục `cmake` cho các tệp CMake hỗ trợ, như các tệp `Find<library>.cmake`. Nhưng những điều cơ bản là có.

Hãy lưu ý một vài điều đã rõ ràng; các tệp `CMakeLists.txt` được chia nhỏ trên tất cả các thư mục nguồn và không có trong các thư mục include. Điều này là do bạn có thể sao chép nội dung của thư mục include trực tiếp vào `/usr/include` hoặc tương tự (ngoại trừ các header cấu hình, mà tôi sẽ trình bày trong một chương khác) và không có bất kỳ tệp bổ sung nào hoặc gây ra bất kỳ xung đột nào. Đó cũng là lý do tại sao có một thư mục cho dự án của bạn bên trong thư mục include. Sử dụng `add_subdirectory` để thêm một thư mục con chứa `CMakeLists.txt`.

Bạn thường muốn có một thư mục `cmake`, với tất cả các module hỗ trợ của bạn. Đây là nơi các tệp `Find*.cmake` của bạn được đặt. Một tập hợp một số trình hỗ trợ phổ biến có tại [github.com/CLIUtils/cmake](https://github.com/CLIUtils/cmake). Để thêm thư mục này vào đường dẫn CMake của bạn:

```cmake
set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})
```

Thư mục `extern` của bạn nên chứa gần như hoàn toàn các git submodule. Bằng cách đó, bạn có thể kiểm soát rõ ràng phiên bản của các dependency, nhưng vẫn có thể dễ dàng nâng cấp. Xem chương Kiểm thử để biết ví dụ về cách thêm submodule.

Bạn nên có một cái gì đó giống như `/build*` trong `.gitignore` của mình, để người dùng có thể tạo các thư mục build trong thư mục nguồn và sử dụng chúng để build. Một số gói cấm điều này, nhưng nó tốt hơn nhiều so với việc thực hiện một bản build out-of-source thực sự và phải nhập một cái gì đó khác nhau cho mỗi gói bạn build.

Nếu bạn muốn tránh thư mục build nằm trong thư mục nguồn hợp lệ, hãy thêm đoạn mã này gần đầu CMakeLists của bạn:

```cmake
### Yêu cầu bản build out-of-source
file(TO_CMAKE_PATH "${PROJECT_BINARY_DIR}/CMakeLists.txt" LOC_PATH)
if(EXISTS "${LOC_PATH}")
    message(FATAL_ERROR "Bạn không thể build trong thư mục nguồn (hoặc bất kỳ thư mục nào có tệp CMakeLists.txt). Vui lòng tạo một thư mục con build. Bạn có thể xóa CMakeCache.txt và CMakeFiles.")
endif()
```

Xem [ví dụ mã mở rộng tại đây](https://gitlab.com/CLIUtils/modern-cmake/tree/master/examples/extended-project).
