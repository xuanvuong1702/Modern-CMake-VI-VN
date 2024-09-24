# Thêm các tính năng

Có rất nhiều cài đặt trình biên dịch và trình liên kết. Khi bạn cần thêm một cái gì đó đặc biệt, trước tiên bạn có thể kiểm tra xem CMake có hỗ trợ nó không; nếu có, bạn có thể tránh việc tự ràng buộc mình với một phiên bản trình biên dịch một cách rõ ràng. Và, tốt hơn nữa, bạn giải thích ý nghĩa của mình trong CMakeLists, thay vì đưa ra các cờ (flag).

Tính năng đầu tiên và phổ biến nhất là hỗ trợ các tiêu chuẩn C++, đã có một chương riêng.

## Mã độc lập vị trí (Position independent code)

[Điều này](https://cmake.org/cmake/help/latest/variable/CMAKE_POSITION_INDEPENDENT_CODE.html) được biết đến nhiều nhất là cờ `-fPIC`. Phần lớn thời gian, bạn không cần phải làm gì cả. CMake sẽ bao gồm cờ cho các thư viện `SHARED` hoặc `MODULE`. Nếu bạn thực sự cần nó một cách rõ ràng:

```cmake
set(CMAKE_POSITION_INDEPENDENT_CODE ON)
```

sẽ thực hiện nó trên toàn cầu, hoặc:

```cmake
set_target_properties(lib1 PROPERTIES POSITION_INDEPENDENT_CODE ON)
```

để bật nó một cách rõ ràng `ON` (hoặc `OFF`) cho một mục tiêu.

## Các thư viện nhỏ

Nếu bạn cần liên kết đến thư viện `dl`, với `-ldl` trên Linux, chỉ cần sử dụng biến CMake tích hợp sẵn [`${CMAKE_DL_LIBS}`](https://cmake.org/cmake/help/latest/variable/CMAKE_DL_LIBS.html) trong lệnh `target_link_libraries`. Không cần mô-đun hoặc `find_package`. (Điều này thêm bất cứ thứ gì cần thiết để lấy `dlopen` và `dlclose`)

Thật không may, thư viện toán học không được may mắn như vậy. Nếu bạn cần liên kết rõ ràng với nó, bạn luôn có thể làm `target_link_libraries(MyTarget PUBLIC m)`, nhưng có thể tốt hơn nếu sử dụng [`find_library`](https://cmake.org/cmake/help/latest/command/find_library.html) chung của CMake:

```cmake
find_library(MATH_LIBRARY m)
if(MATH_LIBRARY)
    target_link_libraries(MyTarget PUBLIC ${MATH_LIBRARY})
endif()
```

Bạn có thể dễ dàng tìm thấy các tệp `Find*.cmake` cho thư viện này và các thư viện khác mà bạn cần bằng cách tìm kiếm nhanh; hầu hết các gói chính đều có thư viện trợ giúp của các mô-đun CMake. Xem chương về bao gồm gói hiện có để biết thêm.

## Tối ưu hóa liên thủ tục (Interprocedural Optimization)

`INTERPROCEDURAL_OPTIMIZATION`, được biết đến nhiều nhất là _tối ưu hóa thời gian liên kết_ (Link-Time Optimization - LTO) và cờ `-flto`, có sẵn trên các phiên bản CMake rất gần đây. Bạn có thể bật tính năng này bằng `CMAKE_INTERPROCEDURAL_OPTIMIZATION` (chỉ CMake 3.9+) hoặc thuộc tính `INTERPROCEDURAL_OPTIMIZATION` trên các mục tiêu. Hỗ trợ cho GCC và Clang đã được thêm vào CMake 3.8. Nếu bạn đặt `cmake_minimum_required(VERSION 3.9)` hoặc cao hơn (xem `CMP0069`), đặt giá trị này thành `ON` trên một mục tiêu sẽ là một lỗi nếu trình biên dịch không hỗ trợ nó. Bạn có thể sử dụng `check_ipo_supported()`, từ mô-đun `CheckIPOSupported` tích hợp sẵn, để xem liệu có hỗ trợ hay không trước khi thực hiện. Ví dụ về cách sử dụng kiểu 3.9:

```cmake
include(CheckIPOSupported)
check_ipo_supported(RESULT result)
if(result)
  set_target_properties(foo PROPERTIES INTERPROCEDURAL_OPTIMIZATION TRUE)
endif()
```