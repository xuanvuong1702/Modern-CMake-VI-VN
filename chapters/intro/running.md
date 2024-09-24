## Chạy CMake

Trước khi viết CMake, hãy đảm bảo bạn biết cách chạy nó để tạo ra các tệp tin cần thiết. Điều này đúng với hầu hết tất cả các dự án CMake, gần như là tất cả mọi thứ.

## Xây dựng một dự án

Trừ khi có ghi chú khác, bạn nên luôn tạo một thư mục build và xây dựng từ đó. Về mặt kỹ thuật, bạn có thể thực hiện build ngay trong thư mục nguồn, nhưng bạn sẽ phải cẩn thận để không ghi đè lên các tệp hoặc thêm chúng vào git, vì vậy tốt nhất là đừng làm vậy.

Dưới đây là Quy trình Xây dựng CMake Cổ điển (TM):

```bash
~/package $ mkdir build
~/package $ cd build
~/package/build $ cmake ..
~/package/build $ make
```

Bạn có thể thay thế dòng `make` bằng `cmake --build .` nếu muốn, và nó sẽ gọi `make` hoặc bất kỳ công cụ build nào bạn đang sử dụng. Nếu bạn đang sử dụng phiên bản CMake mới hơn (thường nên làm, trừ khi bạn cần kiểm tra khả năng tương thích với CMake cũ hơn), bạn có thể làm như sau:

```bash
~/package $ cmake -S . -B build
~/package $ cmake --build build
```

Bất kỳ _một_ lệnh nào trong số này sẽ cài đặt:

```bash
# Từ thư mục build (chọn một)

~/package/build $ make install
~/package/build $ cmake --build . --target install
~/package/build $ cmake --install . # CMake 3.15+ only

# Từ thư mục nguồn (chọn một)

~/package $ make -C build install
~/package $ cmake --build build --target install
~/package $ cmake --install build # CMake 3.15+ only
```

Vậy bạn nên sử dụng phương pháp nào? Miễn là bạn _không quên_ gõ thư mục build làm đối số, việc ở ngoài thư mục build sẽ ngắn gọn hơn và việc thay đổi mã nguồn sẽ dễ dàng hơn từ thư mục nguồn. Bạn nên cố gắng làm quen với việc sử dụng `--build`, vì điều đó sẽ giúp bạn không phải chỉ sử dụng `make` để build. Lưu ý rằng làm việc từ thư mục build theo lịch sử phổ biến hơn nhiều và một số công cụ và lệnh (bao gồm CTest <3.20) vẫn yêu cầu chạy từ thư mục build.

Để làm rõ, bạn có thể trỏ CMake vào thư mục nguồn _từ thư mục build_, hoặc vào thư mục build _đã tồn tại_ từ bất kỳ đâu.

Nếu bạn sử dụng `cmake --build` thay vì gọi trực tiếp hệ thống build bên dưới, bạn có thể sử dụng `-v` để build chi tiết (CMake 3.14+), `-j N` để build song song trên N lõi (CMake 3.12+), và `--target` (bất kỳ phiên bản CMake nào) hoặc `-t` (CMake 3.15+) để chọn một target. Nếu không, các lệnh này sẽ khác nhau giữa các hệ thống build, chẳng hạn như `VERBOSE=1 make` và `ninja -v`. Thay vào đó, bạn cũng có thể sử dụng các biến môi trường cho những điều này, chẳng hạn như `CMAKE_BUILD_PARALLEL_LEVEL` (CMake 3.12+) và `VERBOSE` (CMake 3.14+).

## Chọn trình biên dịch

Việc chọn trình biên dịch phải được thực hiện trong lần chạy đầu tiên trong một thư mục trống. Nó không phải là cú pháp CMake, nhưng bạn có thể không quen thuộc với nó. Để chọn Clang:

```bash
~/package/build $ CC=clang CXX=clang++ cmake ..
```

Điều đó đặt các biến môi trường trong bash cho CC và CXX, và CMake sẽ tôn trọng các biến đó. Điều này chỉ đặt nó cho dòng đó, nhưng đó là lần duy nhất bạn sẽ cần chúng; sau đó CMake tiếp tục sử dụng các đường dẫn mà nó suy ra từ các giá trị đó.

## Chọn một trình tạo (generator)

Bạn có thể build bằng nhiều công cụ khác nhau; `make` thường là mặc định. Để xem tất cả các công cụ CMake biết trên hệ thống của bạn, hãy chạy

```bash
~/package/build $ cmake --help
```

Và bạn có thể chọn một công cụ bằng `-G"My Tool"` (chỉ cần dấu ngoặc kép nếu tên công cụ có khoảng trắng). Bạn nên chọn một công cụ trong lần gọi CMake đầu tiên của mình trong một thư mục, giống như trình biên dịch. 
Bạn có thể đặt biến môi trường `CMAKE_GENERATOR` để kiểm soát trình tạo mặc định (CMake 3.15+).
Lưu ý rằng makefiles sẽ chỉ chạy song song nếu bạn truyền rõ ràng số lượng luồng, chẳng hạn như `make -j2`, trong khi Ninja sẽ tự động chạy song song. Bạn có thể trực tiếp truyền một tùy chọn song song hóa như `-j2` cho lệnh `cmake --build .` trong các phiên bản CMake gần đây.

## Đặt tùy chọn

Bạn đặt các tùy chọn trong CMake bằng `-D`. Bạn có thể xem danh sách các tùy chọn bằng `-L`, hoặc danh sách có trợ giúp dễ đọc bằng `-LH`. 

## Build chi tiết và build một phần

Mặc dù không phải tất cả các công cụ build đều hỗ trợ, nhưng bạn có thể nhận được build chi tiết (chọn một):

```bash
~/package $ cmake --build build --verbose # CMake 3.14+ only
~/package/build $ VERBOSE=1 make
```

Bạn thực sự có thể viết `make VERBOSE=1`, và make cũng sẽ làm điều đúng, mặc dù đó là một tính năng của `make` chứ không phải dòng lệnh nói chung.

Bạn cũng có thể chỉ build một phần của bản build bằng cách chỉ định một target, chẳng hạn như tên của một thư viện hoặc tệp thực thi mà bạn đã định nghĩa trong CMake, và make sẽ chỉ build target đó.

## Tùy chọn

CMake hỗ trợ các tùy chọn được lưu trong bộ nhớ cache. Một biến trong CMake có thể được đánh dấu là "cached", có nghĩa là nó sẽ được ghi vào bộ nhớ cache (một tệp có tên `CMakeCache.txt` trong thư mục build) khi nó được gặp. Bạn có thể đặt trước (hoặc thay đổi) giá trị của một tùy chọn được lưu trong bộ nhớ cache trên dòng lệnh bằng `-D`. Khi CMake tìm kiếm một biến được lưu trong bộ nhớ cache, nó sẽ sử dụng giá trị hiện có và sẽ không ghi đè lên nó.

### Tùy chọn tiêu chuẩn

Đây là những tùy chọn CMake phổ biến cho hầu hết các gói:

- `-DCMAKE_BUILD_TYPE=` Chọn từ Release, RelWithDebInfo, Debug hoặc đôi khi nhiều hơn.
- `-DCMAKE_INSTALL_PREFIX=` Vị trí để cài đặt. Cài đặt hệ thống trên UNIX thường là `/usr/local` (mặc định), thư mục người dùng thường là `~/.local`, hoặc bạn có thể chọn một thư mục.
- `-DBUILD_SHARED_LIBS=` Bạn có thể đặt `ON` hoặc `OFF` này để kiểm soát mặc định cho các thư viện dùng chung (tác giả có thể chọn một trong hai cách rõ ràng thay vì sử dụng mặc định).
- `-DBUILD_TESTING=` Đây là một tên phổ biến để bật kiểm tra, không phải tất cả các gói đều sử dụng nó, mặc dù đôi khi có lý do chính đáng.

## Gỡ lỗi các tệp CMake của bạn

Chúng tôi đã đề cập đến đầu ra chi tiết cho bản build, nhưng bạn cũng có thể xem đầu ra cấu hình CMake chi tiết. Tùy chọn `--trace` sẽ in ra mọi dòng CMake đang chạy. Vì điều này rất dài dòng, CMake 3.7 đã thêm `--trace-source="filename"`, sẽ in ra mọi dòng được thực thi của tệp bạn quan tâm khi nó chạy. Nếu bạn chọn tên của tệp bạn quan tâm đến việc gỡ lỗi (thường bằng cách chọn thư mục mẹ khi gỡ lỗi CMakeLists.txt, vì tất cả chúng đều có cùng tên), bạn chỉ có thể xem các dòng chạy trong tệp đó. Rất hữu ích!
