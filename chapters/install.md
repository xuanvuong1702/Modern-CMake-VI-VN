# Xuất và Cài đặt

Có ba cách tốt và một cách xấu để cho phép người khác sử dụng thư viện của bạn:

## Mô-đun Find (cách xấu)

Nếu bạn là tác giả thư viện, đừng tạo tập lệnh `Find<mypackage>.cmake`! Những thứ này được thiết kế cho các thư viện mà tác giả của chúng không hỗ trợ CMake. Sử dụng `Config<mypackage>.cmake` thay thế như được liệt kê bên dưới.

## Thêm dự án con

Một gói có thể bao gồm dự án của bạn trong một thư mục con, sau đó sử dụng `add_subdirectory` trên thư mục con. Điều này hữu ích cho các thư viện chỉ có tiêu đề và biên dịch nhanh. Lưu ý rằng các lệnh cài đặt có thể can thiệp vào dự án cha, vì vậy bạn có thể thêm `EXCLUDE_FROM_ALL` vào lệnh `add_subdirectory`; các mục tiêu bạn sử dụng rõ ràng vẫn sẽ được xây dựng.

Để hỗ trợ điều này với tư cách là tác giả thư viện, hãy đảm bảo rằng bạn sử dụng `CMAKE_CURRENT_SOURCE_DIR` thay vì `PROJECT_SOURCE_DIR` (và tương tự cho các biến khác, như thư mục nhị phân). Bạn có thể kiểm tra `CMAKE_PROJECT_NAME STREQUAL PROJECT_NAME` để chỉ thêm các tùy chọn hoặc mặc định có ý nghĩa nếu đây là một dự án.

Ngoài ra, vì không gian tên là một ý tưởng hay và việc sử dụng thư viện của bạn phải nhất quán với các phương pháp khác bên dưới, bạn nên thêm

```cmake
add_library(MyLib::MyLib ALIAS MyLib)
```

để chuẩn hóa cách sử dụng trên tất cả các phương pháp. Mục tiêu ALIAS này sẽ không được xuất bên dưới.

## Xuất

Cách thứ ba là các tập lệnh `*Config.cmake`; đó sẽ là chủ đề của chương tiếp theo trong phiên này.
