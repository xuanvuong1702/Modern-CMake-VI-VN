# Thêm các tính năng

Phần này trình bày cách thêm các tính năng phổ biến vào dự án CMake của bạn. Bạn sẽ tìm hiểu cách thêm nhiều tùy chọn thường cần trong các dự án C++, chẳng hạn như hỗ trợ C++11, cũng như cách hỗ trợ IDE và hơn thế nữa.

## Kiểu build mặc định

CMake thường tạo một kiểu build trống "không phải release, không phải debug"; nếu bạn muốn tự mình thiết lập kiểu build mặc định, bạn có thể làm theo công thức này cho kiểu build mặc định được sửa đổi từ [blog Kitware](https://blog.kitware.com/cmake-and-the-default-build-type/):

```cmake
set(default_build_type "Release")
if(NOT CMAKE_BUILD_TYPE AND NOT CMAKE_CONFIGURATION_TYPES)
  message(STATUS "Đặt kiểu build thành '${default_build_type}' vì chưa có kiểu nào được chỉ định.")
  set(CMAKE_BUILD_TYPE "${default_build_type}" CACHE
      STRING "Chọn kiểu build." FORCE)
  # Đặt các giá trị có thể có của kiểu build cho cmake-gui
  set_property(CACHE CMAKE_BUILD_TYPE PROPERTY STRINGS
    "Debug" "Release" "MinSizeRel" "RelWithDebInfo")
endif()
```