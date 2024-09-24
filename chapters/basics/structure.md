# Cách cấu trúc dự án của bạn

Thông tin sau đây mang tính chủ quan. Nhưng theo một cách tốt, tôi nghĩ vậy. Tôi sẽ cho bạn biết cách cấu trúc các thư mục trong dự án của bạn. Điều này dựa trên quy ước, nhưng sẽ giúp bạn:

- Dễ dàng đọc các dự án khác theo cùng một mẫu,
- Tránh một mẫu gây ra xung đột,
- Tránh làm rối và phức tạp bản dựng của bạn.

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

Các tên không phải là tuyệt đối; bạn sẽ thấy tranh cãi về `test/` so với `tests/` và thư mục ứng dụng có thể được gọi là một cái gì đó khác (hoặc không tồn tại đối với dự án chỉ có thư viện). Bạn cũng sẽ đôi khi thấy một thư mục `python` cho các ràng buộc python hoặc một thư mục `cmake` cho các tệp CMake trợ giúp, như các tệp `Find<library>.cmake`. Nhưng những điều cơ bản là có.

Lưu ý một vài điều đã rõ ràng; các tệp `CMakeLists.txt` được chia nhỏ trên tất cả các thư mục nguồn và không có trong các thư mục include. Điều này là do bạn có thể sao chép nội dung của thư mục include trực tiếp vào `/usr/include` hoặc tương tự (ngoại trừ các tiêu đề cấu hình, mà tôi sẽ trình bày trong một chương khác) và không có bất kỳ tệp bổ sung nào hoặc gây ra bất kỳ xung đột nào. Đó cũng là lý do tại sao có một thư mục cho dự án của bạn bên trong thư mục include. Sử dụng `add_subdirectory` để thêm một thư mục con chứa `CMakeLists.txt`.

Bạn thường muốn có một thư mục `cmake`, với tất cả các mô-đun trợ giúp của bạn. Đây là nơi các tệp `Find*.cmake` của bạn đi. Một tập hợp một số trình trợ giúp phổ biến có tại [github.com/CLIUtils/cmake](https://github.com/CLIUtils/cmake). Để thêm thư mục này vào đường dẫn CMake của bạn:

```cmake
set(CMAKE_MODULE_PATH "${PROJECT_SOURCE_DIR}/cmake" ${CMAKE_MODULE_PATH})
```

Thư mục `extern` của bạn nên chứa gần như hoàn toàn các mô-đun con git. Bằng cách đó, bạn có thể kiểm soát rõ ràng phiên bản của các phụ thuộc, nhưng vẫn có thể dễ dàng nâng cấp. Xem chương Kiểm tra để biết ví dụ về cách thêm mô-đun con.

Bạn nên có một cái gì đó giống như `/build*` trong `.gitignore` của mình, để người dùng có thể tạo các thư mục bản dựng trong thư mục nguồn và sử dụng chúng để bản dựng. Một số gói cấm điều này, nhưng nó tốt hơn nhiều so với việc thực hiện bản dựng thực sự ngoài nguồn và phải nhập một cái gì đó khác nhau cho mỗi gói bạn bản dựng.

Nếu bạn muốn tránh thư mục bản dựng nằm trong thư mục nguồn hợp lệ, hãy thêm điều này gần đầu CMakeLists của bạn:

```cmake
### Yêu cầu bản dựng ngoài nguồn
file(TO_CMAKE_PATH "${PROJECT_BINARY_DIR}/CMakeLists.txt" LOC_PATH)
if(EXISTS "${LOC_PATH}")
    message(FATAL_ERROR "Bạn không thể bản dựng trong thư mục nguồn (hoặc bất kỳ thư mục nào có tệp CMakeLists.txt). Vui lòng tạo một thư mục con bản dựng. Bạn có thể xóa CMakeCache.txt và CMakeFiles.")
endif()
```

Xem [ví dụ mã mở rộng tại đây](https://gitlab.com/CLIUtils/modern-cmake/tree/master/examples/extended-project).
