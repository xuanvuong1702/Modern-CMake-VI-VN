## MPI

Để thêm MPI, giống như OpenMP, bạn sẽ làm tốt nhất với CMake 3.9+.

```cmake
find_package(MPI REQUIRED)
message(STATUS "Run: ${MPIEXEC} ${MPIEXEC_NUMPROC_FLAG} ${MPIEXEC_MAX_NUMPROCS} ${MPIEXEC_PREFLAGS} EXECUTABLE ${MPIEXEC_POSTFLAGS} ARGS")
target_link_libraries(MyTarget PUBLIC MPI::MPI_CXX)
```

Tuy nhiên, bạn có thể mô phỏng điều này trên CMake 3.1+ với:

```cmake
find_package(MPI REQUIRED)

# Để hỗ trợ CMake < 3.9:

if(NOT TARGET MPI::MPI_CXX)
    add_library(MPI::MPI_CXX IMPORTED INTERFACE)

    set_property(TARGET MPI::MPI_CXX
                 PROPERTY INTERFACE_COMPILE_OPTIONS ${MPI_CXX_COMPILE_FLAGS})
    set_property(TARGET MPI::MPI_CXX
                 PROPERTY INTERFACE_INCLUDE_DIRECTORIES "${MPI_CXX_INCLUDE_PATH}")
    set_property(TARGET MPI::MPI_CXX
                 PROPERTY INTERFACE_LINK_LIBRARIES ${MPI_CXX_LINK_FLAGS} ${MPI_CXX_LIBRARIES})
endif()

message(STATUS "Run: ${MPIEXEC} ${MPIEXEC_NUMPROC_FLAG} ${MPIEXEC_MAX_NUMPROCS} ${MPIEXEC_PREFLAGS} EXECUTABLE ${MPIEXEC_POSTFLAGS} ARGS")
target_link_libraries(MyTarget PUBLIC MPI::MPI_CXX)

```

**Dịch:**

Để thêm MPI, tương tự như OpenMP, tốt nhất bạn nên sử dụng CMake 3.9 trở lên.

```cmake
find_package(MPI REQUIRED)
message(STATUS "Run: ${MPIEXEC} ${MPIEXEC_NUMPROC_FLAG} ${MPIEXEC_MAX_NUMPROCS} ${MPIEXEC_PREFLAGS} EXECUTABLE ${MPIEXEC_POSTFLAGS} ARGS")
target_link_libraries(MyTarget PUBLIC MPI::MPI_CXX)
```

Tuy nhiên, bạn có thể mô phỏng điều này trên CMake 3.1+ bằng cách:

```cmake
find_package(MPI REQUIRED)

# Để hỗ trợ CMake < 3.9:

if(NOT TARGET MPI::MPI_CXX)
    add_library(MPI::MPI_CXX IMPORTED INTERFACE)

    set_property(TARGET MPI::MPI_CXX
                 PROPERTY INTERFACE_COMPILE_OPTIONS ${MPI_CXX_COMPILE_FLAGS})
    set_property(TARGET MPI::MPI_CXX
                 PROPERTY INTERFACE_INCLUDE_DIRECTORIES "${MPI_CXX_INCLUDE_PATH}")
    set_property(TARGET MPI::MPI_CXX
                 PROPERTY INTERFACE_LINK_LIBRARIES ${MPI_CXX_LINK_FLAGS} ${MPI_CXX_LIBRARIES})
endif()

message(STATUS "Run: ${MPIEXEC} ${MPIEXEC_NUMPROC_FLAG} ${MPIEXEC_MAX_NUMPROCS} ${MPIEXEC_PREFLAGS} EXECUTABLE ${MPIEXEC_POSTFLAGS} ARGS")
target_link_libraries(MyTarget PUBLIC MPI::MPI_CXX)

```

**Giải thích:**

Đoạn mã này hướng dẫn cách thêm hỗ trợ cho MPI (Message Passing Interface), một thư viện dùng để lập trình song song, vào dự án CMake của bạn. 

- `find_package(MPI REQUIRED)`: Tìm kiếm gói MPI trên hệ thống. `REQUIRED` cho biết dự án sẽ dừng lại nếu không tìm thấy MPI.
- `message(STATUS ...)`: In ra thông báo hướng dẫn cách chạy chương trình MPI.
- `target_link_libraries(MyTarget PUBLIC MPI::MPI_CXX)`: Liên kết thư viện `MPI::MPI_CXX` với target `MyTarget`.

Đoạn mã thứ hai cung cấp cách tương thích với các phiên bản CMake cũ hơn (3.1+). Nó kiểm tra xem target `MPI::MPI_CXX` đã tồn tại chưa. Nếu chưa, nó sẽ tự tạo target này và thiết lập các thuộc tính cần thiết như tùy chọn biên dịch, thư mục include và thư viện liên kết.


