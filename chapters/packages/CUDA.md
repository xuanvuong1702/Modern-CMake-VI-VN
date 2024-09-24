## CUDA

Hỗ trợ CUDA có sẵn trong hai dạng. Phương pháp mới, được giới thiệu trong CMake 3.8 (3.9 cho Windows), nên được ưu tiên hơn phương pháp cũ, - Tôi chỉ đề cập đến phương pháp cũ do khả năng cao là một gói cũ ở đâu đó đang sử dụng nó. Không giống như các ngôn ngữ cũ hơn, hỗ trợ CUDA đã phát triển nhanh chóng và việc xây dựng CUDA rất khó, vì vậy tôi khuyên bạn nên _yêu cầu phiên bản CMake rất gần đây_! CMake 3.17 và 3.18 có rất nhiều cải tiến nhắm trực tiếp vào CUDA.

Một nguồn tài nguyên tốt cho CUDA và Modern CMake là [bài nói chuyện này](http://on-demand.gputechconf.com/gtc/2017/presentation/S7438-robert-maynard-build-systems-combining-cuda-and-machine-learning.pdf) của nhà phát triển CMake Robert Maynard tại GTC 2017.

## Thêm Ngôn ngữ CUDA

Có hai cách để bật hỗ trợ CUDA. Nếu CUDA không phải là tùy chọn:

```cmake
project(MY_PROJECT LANGUAGES CUDA CXX)
```

Bạn có thể cũng muốn `CXX` được liệt kê ở đây. Và, nếu CUDA là tùy chọn, bạn sẽ muốn đặt điều này ở đâu đó có điều kiện:

```cmake
enable_language(CUDA)
```

Để kiểm tra xem CUDA có sẵn hay không, hãy sử dụng CheckLanuage:

```cmake
include(CheckLanguage)
check_language(CUDA)
```

Bạn có thể xem CUDA có mặt hay không bằng cách kiểm tra `CMAKE_CUDA_COMPILER` (đã bị thiếu cho đến CMake 3.11).

Bạn có thể kiểm tra các biến như `CMAKE_CUDA_COMPILER_ID` (đối với nvcc, đây là `"NVIDIA"`, Clang đã được thêm vào trong CMake 3.18). Bạn có thể kiểm tra phiên bản bằng `CMAKE_CUDA_COMPILER_VERSION`.

## Biến cho CUDA

Nhiều biến có `CXX` trong tên có phiên bản CUDA với `CUDA` thay thế. Ví dụ: để đặt tiêu chuẩn C++ cần thiết cho CUDA,

```
if(NOT DEFINED CMAKE_CUDA_STANDARD)
    set(CMAKE_CUDA_STANDARD 11)
    set(CMAKE_CUDA_STANDARD_REQUIRED ON)
endif()
```

Nếu bạn đang tìm kiếm mức tiêu chuẩn của CUDA, trong CMake 3.17, một bộ sưu tập các tính năng trình biên dịch mới đã được thêm vào, như `cuda_std_11`. Những điều này có những lợi ích tương tự mà bạn đã quen thuộc từ các phiên bản `cxx`.

### Thêm thư viện / tệp thực thi

Đây là phần dễ dàng; miễn là bạn sử dụng `.cu` cho các tệp CUDA, bạn có thể thêm thư viện _chính xác như bình thường_.

Bạn cũng có thể sử dụng biên dịch tách biệt:

```cmake
set_target_properties(mylib PROPERTIES
                            CUDA_SEPARABLE_COMPILATION ON)
```

Bạn cũng có thể trực tiếp tạo tệp PTX bằng thuộc tính `CUDA_PTX_COMPILATION`.

### Nhắm mục tiêu kiến trúc

Khi bạn xây dựng mã CUDA, bạn thường nên nhắm mục tiêu một kiến trúc. Nếu không, bạn sẽ biên dịch PTX cho kiến trúc được hỗ trợ thấp nhất, cung cấp các hướng dẫn cơ bản nhưng được biên dịch trong thời gian chạy, khiến nó có khả năng tải chậm hơn nhiều.

Tất cả các card đều có cấp độ kiến trúc, như "7.2". Bạn có hai lựa chọn; đầu tiên là cấp độ mã; điều này sẽ báo cáo cho mã đang được biên dịch một phiên bản, như "5.0", và nó sẽ tận dụng tất cả các tính năng lên đến 5.0 nhưng không quá (giả sử mã được viết tốt / thư viện tiêu chuẩn). Sau đó, có một kiến trúc mục tiêu, phải bằng hoặc lớn hơn kiến trúc mã. Điều này cần phải có cùng số chính với card mục tiêu của bạn và bằng hoặc nhỏ hơn card mục tiêu. Vì vậy, 7.0 sẽ là một lựa chọn phổ biến cho card 7.2 của chúng tôi. Cuối cùng, bạn cũng có thể tạo PTX; điều này sẽ hoạt động trên tất cả các card trong tương lai, nhưng sẽ được biên dịch ngay đúng lúc.

Trong CMake 3.18, việc nhắm mục tiêu kiến trúc trở nên rất dễ dàng. Nếu bạn có một phạm vi phiên bản bao gồm 3.18 trở lên, bạn sẽ sử dụng biến `CMAKE_CUDA_ARCHITECTURES` và thuộc tính `CUDA_ARCHITECTURES` trên các target. Bạn có thể liệt kê các giá trị (không có `.`), như 50 cho arch 5.0. Điều này sẽ tạo cho cả kiến trúc thực (SASS) và kiến trúc ảo (PTX). Việc truyền các giá trị của '50-real' sẽ chỉ tạo cho SASS, trong khi việc truyền '50-virtual' sẽ chỉ tạo cho PTX. Nếu được đặt thành OFF, nó sẽ không truyền kiến trúc.

Trong CMake 3.24, các giá trị kiến trúc đã được mở rộng để hỗ trợ các giá trị thân thiện với người dùng là 'native', 'all' và 'all-major'.

### Làm việc với các target

Sử dụng target sẽ hoạt động tương tự như CXX, nhưng có một vấn đề. Nếu bạn bao gồm một target bao gồm các tùy chọn trình biên dịch (cờ), hầu hết thời gian, các tùy chọn sẽ không được bảo vệ bởi các include chính xác (và khả năng chúng có trình bao bọc CUDA chính xác thậm chí còn nhỏ hơn). Đây là dòng tùy chọn trình biên dịch chính xác sẽ trông như thế nào:

```cmake
set(opt "$<$<BUILD_INTERFACE:$<COMPILE_LANGUAGE:CXX>>:-fopenmp>$<$<BUILD_INTERFACE:$<COMPILE_LANGUAGE:CUDA>>:-Xcompiler=-fopenmp>")
```

Tuy nhiên, nếu bạn sử dụng gần như bất kỳ find_package nào và sử dụng các phương pháp Modern CMake của target và kế thừa, mọi thứ sẽ bị hỏng. Tôi đã học được điều đó một cách khó khăn.

Hiện tại, đây là một giải pháp khá hợp lý, _miễn là bạn biết tên target không được đặt bí danh_. Đó là một hàm sẽ sửa một target chỉ C++ bằng cách bao bọc các cờ nếu sử dụng trình biên dịch CUDA:

```cmake
function(CUDA_CONVERT_FLAGS EXISTING_TARGET)
    get_property(old_flags TARGET ${EXISTING_TARGET} PROPERTY INTERFACE_COMPILE_OPTIONS)
    if(NOT "${old_flags}" STREQUAL "")
        string(REPLACE ";" "," CUDA_flags "${old_flags}")
        set_property(TARGET ${EXISTING_TARGET} PROPERTY INTERFACE_COMPILE_OPTIONS
            "$<$<BUILD_INTERFACE:$<COMPILE_LANGUAGE:CXX>>:${old_flags}>$<$<BUILD_INTERFACE:$<COMPILE_LANGUAGE:CUDA>>:-Xcompiler=${CUDA_flags}>"
            )
    endif()
endfunction()
```

### Các biến hữu ích

Bạn có thể sử dụng
[`FindCUDAToolkit`](https://cmake.org/cmake/help/git-stage/module/FindCUDAToolkit.html)
để tìm nhiều target và biến hữu ích ngay cả khi không bật ngôn ngữ CUDA.

```cmake
cmake_minimum_required(VERSION 3.17)
project(example LANGUAGES CXX)

find_package(CUDAToolkit REQUIRED)
add_executable(uses_cublas source.cpp)
target_link_libraries(uses_cublas PRIVATE CUDA::cublas)
```

Các biến mà việc sử dụng `find_package(CUDAToolkit)` cung cấp:

- `CUDAToolkit_BIN_DIR`: Thư mục chứa tệp thực thi `nvcc`
- `CUDAToolkit_INCLUDE_DIRS`: Danh sách các thư mục chứa header cho Thrust tích hợp sẵn, v.v.
- `CUDAToolkit_LIBRARY_DIR`: Thư mục chứa thư viện thời gian chạy CUDA

Các biến mà việc bật ngôn ngữ `CUDA` cung cấp:

- `CMAKE_CUDA_COMPILER`: NVCC với vị trí
- `CMAKE_CUDA_TOOLKIT_INCLUDE_DIRECTORIES`: Vị trí cho Thrust tích hợp sẵn, v.v.

> ### Lưu ý rằng FindCUDA đã bị phản đối, nhưng đối với các phiên bản CMake < 3.18, các hàm sau yêu cầu FindCUDA:
>
> - Kiểm tra phiên bản CUDA / chọn phiên bản
> - Phát hiện kiến trúc (Lưu ý: 3.12 khắc phục một phần điều này)
> - Liên kết đến các thư viện CUDA từ các tệp không phải .cu

## FindCUDA cổ điển [CẢNH BÁO: KHÔNG SỬ DỤNG] (chỉ để tham khảo)

Nếu bạn muốn hỗ trợ phiên bản CMake cũ hơn, tôi khuyên bạn nên ít nhất bao gồm FindCUDA từ phiên bản CMake 3.9 trong thư mục cmake của bạn (xem tổ chức github CLIUtils để biết [kho lưu trữ git](https://github.com/CLIUtils/cuda_support)). Bạn sẽ muốn hai tính năng đã được thêm vào: `CUDA_LINK_LIBRARIES_KEYWORD` và `cuda_select_nvcc_arch_flags`, cùng với các kiến trúc và phiên bản CUDA mới hơn.

Để sử dụng hỗ trợ CUDA cũ, bạn sử dụng `find_package`:

```cmake
find_package(CUDA 7.0 REQUIRED)
message(STATUS "Found CUDA ${CUDA_VERSION_STRING} at ${CUDA_TOOLKIT_ROOT_DIR}")
```

Bạn có thể kiểm soát các cờ CUDA bằng `CUDA_NVCC_FLAGS` (danh sách nối thêm) và bạn có thể kiểm soát quá trình biên dịch tách biệt bằng `CUDA_SEPARABLE_COMPILATION`. Bạn cũng sẽ muốn đảm bảo CUDA hoạt động tốt và thêm các từ khóa vào target (CMake 3.9+):

```cmake
set(CUDA_LINK_LIBRARIES_KEYWORD PUBLIC)
```

Bạn cũng có thể muốn cho phép người dùng kiểm tra các cờ arch của phần cứng hiện tại của họ:

```cmake
cuda_select_nvcc_arch_flags(ARCH_FLAGS) # đối số tùy chọn cho arch để thêm
```