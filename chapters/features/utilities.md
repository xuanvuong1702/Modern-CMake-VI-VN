# CCache và các tiện ích

Qua các phiên bản, các tiện ích phổ biến giúp bạn viết mã tốt đã được hỗ trợ thêm vào CMake. Điều này thường ở dạng một thuộc tính và biến khởi tạo `CMAKE_*` phù hợp. Tính năng này không có nghĩa là bị ràng buộc với một chương trình đặc biệt nào đó, mà là bất kỳ chương trình nào có hành vi tương tự.

Tất cả những điều này đều nhận các giá trị được phân tách bằng dấu chấm phẩy (một danh sách tiêu chuẩn trong CMake) mô tả chương trình và các tùy chọn mà bạn nên chạy trên các tệp nguồn của mục tiêu này.

## CCache

Đặt biến `CMAKE_<LANG>_COMPILER_LAUNCHER` hoặc thuộc tính `<LANG>_COMPILER_LAUNCHER` trên một mục tiêu để sử dụng một cái gì đó như CCache để "bao bọc" quá trình biên dịch của mục tiêu. Hỗ trợ cho CCache đã được mở rộng trong các phiên bản CMake mới nhất. Trong thực tế, điều này có xu hướng trông như thế này:

```cmake
find_program(CCACHE_PROGRAM ccache)
if(CCACHE_PROGRAM)
    set(CMAKE_CXX_COMPILER_LAUNCHER "${CCACHE_PROGRAM}")
    set(CMAKE_CUDA_COMPILER_LAUNCHER "${CCACHE_PROGRAM}") # CMake 3.9+
endif()
```

## Các tiện ích

Đặt các thuộc tính sau hoặc biến khởi tạo `CMAKE_*` thành dòng lệnh cho các công cụ. Hầu hết chúng bị giới hạn ở C hoặc CXX với các trình tạo make hoặc ninja.

- `<LANG>_CLANG_TIDY`: CMake 3.6+
- `<LANG>_CPPCHECK`
- `<LANG>_CPPLINT`
- `<LANG>_INCLUDE_WHAT_YOU_USE`

## Clang tidy

Đây là dòng lệnh để chạy clang-tidy, dưới dạng danh sách (hãy nhớ rằng, một chuỗi được phân tách bằng dấu chấm phẩy là một danh sách).

Dưới đây là một ví dụ đơn giản về việc sử dụng Clang-Tidy:

```bash
~/package # cmake -S . -B build-tidy -DCMAKE_CXX_CLANG_TIDY="$(which clang-tidy);-fix"
~/package # cmake --build build -j 1
```

Phần `-fix` là tùy chọn và sẽ sửa đổi các tệp nguồn của bạn để thử sửa 
các cảnh báo do clang-tidy đưa ra. Nếu bạn đang làm việc trong kho lưu trữ git, điều này khá
an toàn vì bạn có thể thấy những gì đã thay đổi. Tuy nhiên, hãy đảm bảo rằng bạn **không chạy
bản dựng makefile/ninja của bạn song song**! Điều này sẽ không hoạt động tốt chút nào nếu nó
cố gắng sửa cùng một header hai lần.

Nếu bạn muốn sử dụng rõ ràng dạng mục tiêu để đảm bảo rằng bạn chỉ gọi điều này trên
các mục tiêu cục bộ của mình, bạn có thể đặt một biến (có thể là một cái gì đó giống như
`DO_CLANG_TIDY`) thay vì biến `CMAKE_CXX_CLANG_TIDY`, sau đó thêm nó vào
thuộc tính mục tiêu của bạn khi bạn tạo chúng. Bạn có thể tìm clang-tidy trong đường dẫn của bạn
như thế này:

```cmake
find_program(
    CLANG_TIDY_EXE
    NAMES "clang-tidy"
    DOC "Đường dẫn đến tệp thực thi clang-tidy"
)
```

## Include what you use

Đây là một ví dụ để sử dụng include what you use. Đầu tiên, bạn sẽ cần phải có
công cụ, chẳng hạn như trong container docker hoặc với brew (macOS) với `brew install include-what-you-use`. Sau đó, bạn có thể chuyển điều này vào bản dựng của mình mà không cần
sửa đổi mã nguồn:

```bash
~/package # cmake -S . -B build-iwyu -DCMAKE_CXX_INCLUDE_WHAT_YOU_USE=include-what-you-use
```

Cuối cùng, bạn có thể thu thập đầu ra và (tùy chọn) áp dụng các bản sửa lỗi:

```bash
~/package # cmake --build build-iwyu 2> iwyu.out
~/package # fix_includes.py < iwyu.out
```

(Bạn nên kiểm tra các bản sửa lỗi trước hoặc chỉnh sửa chúng sau khi áp dụng!)

## Link what you use

Có một thuộc tính mục tiêu boolean, `LINK_WHAT_YOU_USE`, sẽ kiểm tra các tệp không cần thiết khi liên kết.

## Clang-format

Thật không may, Clang-format không thực sự tích hợp với CMake. Bạn có thể tạo một mục tiêu tùy chỉnh (Xem [bài đăng này](https://arcanis.me/en/2015/10/17/cppcheck-and-clang-format)), hoặc bạn có thể chạy nó theo cách thủ công. Một dự án thú vị mà tôi chưa thực sự thử nghiệm là [ở đây](https://github.com/kbenzie/git-cmake-format); nó thêm một mục tiêu định dạng và thậm chí đảm bảo rằng bạn không thể commit các tệp không được định dạng.

Hai dòng sau sẽ làm điều đó trong kho lưu trữ git trong bash (giả sử bạn có tệp `.clang-format`):

```bash
$ git ls-files -- '*.cpp' '*.h' | xargs clang-format -i -style=file
$ git diff --exit-code --color
```

