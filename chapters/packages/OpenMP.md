## OpenMP

Hỗ trợ [OpenMP] đã được cải thiện đáng kể trong CMake 3.9 trở lên. Cách hiện đại (Modern™) để thêm OpenMP vào một target là:

```cmake
find_package(OpenMP)
if(OpenMP_CXX_FOUND)
    target_link_libraries(MyTarget PUBLIC OpenMP::OpenMP_CXX)
endif()
```

Cách này không chỉ gọn gàng hơn phương pháp cũ mà còn thiết lập chính xác dòng liên kết thư viện khác với dòng biên dịch nếu cần. Trong CMake 3.12 trở lên, cách này thậm chí còn hỗ trợ OpenMP trên macOS (nếu thư viện có sẵn, chẳng hạn như với `brew install libomp`). Tuy nhiên, nếu bạn cần hỗ trợ CMake cũ hơn, đoạn mã sau hoạt động trên CMake 3.1+:

```cmake
# Đối với CMake < 3.9, chúng ta cần tự tạo target
if(NOT TARGET OpenMP::OpenMP_CXX)
    find_package(Threads REQUIRED)
    add_library(OpenMP::OpenMP_CXX IMPORTED INTERFACE)
    set_property(TARGET OpenMP::OpenMP_CXX
                 PROPERTY INTERFACE_COMPILE_OPTIONS ${OpenMP_CXX_FLAGS})
    # Chỉ hoạt động nếu cùng một cờ được truyền cho trình liên kết; nếu không, hãy sử dụng CMake 3.9+ (Intel, AppleClang)
    set_property(TARGET OpenMP::OpenMP_CXX
                 PROPERTY INTERFACE_LINK_LIBRARIES ${OpenMP_CXX_FLAGS} Threads::Threads)

endif()
target_link_libraries(MyTarget PUBLIC OpenMP::OpenMP_CXX)
```

::: {danger}

CMake < 3.4 có một lỗi trong gói Threads yêu cầu bạn phải bật ngôn ngữ `C`.

:::

[openmp]: https://cmake.org/cmake/help/latest/module/FindOpenMP.html


**Giải thích:**

OpenMP là một API (Application Programming Interface) dùng để lập trình song song trên các hệ thống chia sẻ bộ nhớ. Đoạn mã này hướng dẫn cách thêm hỗ trợ OpenMP vào dự án CMake của bạn.

**Cách hiện đại (CMake 3.9+):**

- `find_package(OpenMP)`: Tìm kiếm gói OpenMP trên hệ thống.
- `if(OpenMP_CXX_FOUND)`: Kiểm tra xem OpenMP có được tìm thấy hay không.
- `target_link_libraries(MyTarget PUBLIC OpenMP::OpenMP_CXX)`: Liên kết thư viện OpenMP với target `MyTarget`.

**Cách tương thích với CMake cũ hơn (3.1+):**

- `if(NOT TARGET OpenMP::OpenMP_CXX)`: Kiểm tra xem target `OpenMP::OpenMP_CXX` đã tồn tại chưa.
- `find_package(Threads REQUIRED)`: Tìm kiếm gói Threads, cần thiết cho OpenMP.
- `add_library(OpenMP::OpenMP_CXX IMPORTED INTERFACE)`: Tạo một target OpenMP giả làm thư viện import.
- `set_property(...)`: Thiết lập các thuộc tính cho target OpenMP, bao gồm các tùy chọn biên dịch và thư viện liên kết.
- `target_link_libraries(MyTarget PUBLIC OpenMP::OpenMP_CXX)`: Liên kết target OpenMP với target `MyTarget`.

**Lưu ý:** 

- CMake phiên bản dưới 3.4 có một lỗi trong gói Threads, yêu cầu bạn phải bật ngôn ngữ C trong dự án.
- Cách hiện đại (CMake 3.9+) được khuyến nghị sử dụng vì nó đơn giản hơn và xử lý việc liên kết thư viện chính xác hơn. 
