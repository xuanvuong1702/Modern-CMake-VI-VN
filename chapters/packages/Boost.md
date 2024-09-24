## Thư viện Boost

Thư viện Boost được bao gồm trong các gói tìm kiếm mà CMake cung cấp, nhưng nó có một vài điểm kỳ lạ trong cách thức hoạt động. Xem [FindBoost] để biết mô tả đầy đủ; phần này sẽ chỉ cung cấp một cái nhìn tổng quan nhanh và một công thức sử dụng. Hãy chắc chắn kiểm tra trang để biết phiên bản CMake tối thiểu cần thiết mà bạn đang sử dụng và xem bạn có những tùy chọn nào.

Đầu tiên, bạn có thể tùy chỉnh hành vi của các thư viện Boost được chọn bằng cách sử dụng một tập hợp các biến mà bạn đặt trước khi tìm kiếm Boost. Có một số lượng ngày càng tăng các cài đặt, nhưng đây là ba cài đặt phổ biến nhất:

```cmake
set(Boost_USE_STATIC_LIBS OFF)
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_RUNTIME OFF)
```

Trong CMake 3.5, các target được import đã được thêm vào. Các target này cũng xử lý các dependency cho bạn, vì vậy chúng là một cách rất hay để thêm các thư viện Boost. Tuy nhiên, CMake có thông tin dependency được tích hợp sẵn cho tất cả các phiên bản Boost đã biết, vì vậy CMake phải mới hơn Boost để chúng hoạt động. Trong một [yêu cầu hợp nhất] gần đây [mroldboost], CMake đã bắt đầu giả định rằng các dependency được giữ từ phiên bản cuối cùng mà nó biết, và sẽ sử dụng điều đó (cùng với việc đưa ra cảnh báo). Chức năng này đã được backport vào CMake 3.9.

Các target import nằm trong namespace `Boost::`. `Boost::boost` là phần chỉ dành cho header. Các thư viện được biên dịch khác có sẵn và bao gồm các dependency khi cần thiết.

Dưới đây là một ví dụ về cách sử dụng thư viện `Boost::filesystem`:

```cmake
set(Boost_USE_STATIC_LIBS OFF)
set(Boost_USE_MULTITHREADED ON)
set(Boost_USE_STATIC_RUNTIME OFF)
find_package(Boost 1.50 REQUIRED COMPONENTS filesystem)
message(STATUS "Boost version: ${Boost_VERSION}")

# Điều này là cần thiết nếu phiên bản Boost của bạn mới hơn phiên bản CMake của bạn
# hoặc nếu bạn có phiên bản CMake cũ (<3.5)
if(NOT TARGET Boost::filesystem)
    add_library(Boost::filesystem IMPORTED INTERFACE)
    set_property(TARGET Boost::filesystem PROPERTY
        INTERFACE_INCLUDE_DIRECTORIES ${Boost_INCLUDE_DIR})
    set_property(TARGET Boost::filesystem PROPERTY
        INTERFACE_LINK_LIBRARIES ${Boost_LIBRARIES})
endif()

target_link_libraries(MyExeOrLibrary PUBLIC Boost::filesystem)
```

[findboost]: https://cmake.org/cmake/help/latest/module/FindBoost.html
[mroldboost]: https://gitlab.kitware.com/cmake/cmake/merge_requests/1172