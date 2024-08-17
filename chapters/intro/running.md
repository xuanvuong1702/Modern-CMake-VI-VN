# Chạy CMake

Trước khi viết CMake, hãy đảm bảo bạn biết cách chạy nó để tạo ra các sản phẩm. Điều này đúng với hầu hết các dự án CMake, tức là hầu hết mọi thứ.

## Xây dựng một dự án

Trừ khi có ghi chú khác, bạn nên luôn tạo một thư mục build và xây dựng từ đó. Về mặt kỹ thuật, bạn có thể xây dựng trực tiếp trong thư mục nguồn, nhưng bạn sẽ phải cẩn thận để không ghi đè lên các tệp hoặc thêm chúng vào git, vì vậy tốt nhất là không nên làm vậy.

Đây là Quy Trình Xây Dựng CMake Cổ Điển (TM):

```bash
~/package $ mkdir build
~/package $ cd build
~/package/build $ cmake ..
~/package/build $ make
```

Bạn có thể thay thế dòng make bằng `cmake --build .` nếu bạn muốn, và nó sẽ gọi `make` hoặc bất kỳ công cụ xây dựng nào bạn đang sử dụng. Nếu bạn đang sử dụng phiên bản mới hơn của CMake (mà bạn thường nên sử dụng, trừ khi kiểm tra tính tương thích với CMake cũ hơn), bạn có thể làm như sau:

```bash
~/package $ cmake -S . -B build
~/package $ cmake --build build
```

Bất kỳ _một_ trong những lệnh này sẽ cài đặt:

```bash
# Từ thư mục build (chọn một)

~/package/build $ make install
~/package/build $ cmake --build . --target install
~/package/build $ cmake --install . # Chỉ CMake 3.15+ 

# Từ thư mục nguồn (chọn một)

~/package $ make -C build install
~/package $ cmake --build build --target install
~/package $ cmake --install build # Chỉ CMake 3.15+ 
```
Vậy bạn nên sử dụng bộ phương pháp nào? Miễn là bạn _không quên_ nhập thư mục build làm đối số, việc làm việc ngoài thư mục build sẽ ngắn gọn hơn, và việc thay đổi mã nguồn sẽ dễ dàng hơn từ thư mục nguồn. Bạn nên cố gắng làm quen với việc sử dụng `--build`, vì điều đó sẽ giúp bạn không chỉ sử dụng `make` để xây dựng. Lưu ý rằng làm việc từ thư mục build lịch sử phổ biến hơn nhiều, và một số công cụ và lệnh (bao gồm CTest <3.20) vẫn yêu cầu chạy từ thư mục build.

Để làm rõ, bạn có thể chỉ định CMake vào thư mục nguồn _từ thư mục build_, hoặc vào một thư mục build _đã tồn tại_ từ bất kỳ đâu.

Nếu bạn sử dụng `cmake --build` thay vì gọi trực tiếp hệ thống build cơ bản, bạn có thể sử dụng `-v` cho các build chi tiết (CMake 3.14+), `-j N` cho các build song song trên N lõi (CMake 3.12+), và `--target` (bất kỳ phiên bản CMake nào) hoặc `-t` (CMake 3.15+) để chọn một mục tiêu. Nếu không, các lệnh này sẽ khác nhau giữa các hệ thống build, chẳng hạn như `VERBOSE=1 make` và `ninja -v`. Bạn cũng có thể sử dụng các biến môi trường cho những điều này, chẳng hạn như `CMAKE_BUILD_PARALLEL_LEVEL` (CMake 3.12+) và `VERBOSE` (CMake 3.14+).

## Chọn trình biên dịch

Chọn trình biên dịch phải được thực hiện trong lần chạy đầu tiên trong một thư mục trống. Đây không phải là cú pháp của CMake, nhưng bạn có thể không quen thuộc với nó. Để chọn Clang:

```bash
~/package/build $ CC=clang CXX=clang++ cmake ..
```

Điều đó đặt các biến môi trường trong bash cho CC và CXX, và CMake sẽ tôn trọng những biến đó. Điều này chỉ đặt nó cho dòng lệnh đó, nhưng đó là lần duy nhất bạn cần những biến đó; sau đó CMake sẽ tiếp tục sử dụng các đường dẫn mà nó suy ra từ những giá trị đó.

## Chọn bộ tạo

Bạn có thể xây dựng với nhiều công cụ khác nhau; `make` thường là mặc định. Để xem tất cả các công cụ mà CMake biết trên hệ thống của bạn, hãy chạy

```bash
~/package/build $ cmake --help
```

Và bạn có thể chọn một công cụ với `-G"Tên Công Cụ"` (dấu ngoặc kép chỉ cần thiết nếu tên công cụ có khoảng trắng). Bạn nên chọn một công cụ trong lần gọi CMake đầu tiên trong một thư mục, giống như trình biên dịch. Hãy thoải mái tạo nhiều thư mục build, như `build/` và `buildXcode`.
Bạn có thể đặt biến môi trường `CMAKE_GENERATOR` để kiểm soát bộ tạo mặc định (CMake 3.15+).
Lưu ý rằng các makefile sẽ chỉ chạy song song nếu bạn truyền rõ ràng số luồng, chẳng hạn như `make -j2`, trong khi Ninja sẽ tự động chạy song song. Bạn có thể trực tiếp truyền tùy chọn song song như `-j2` vào lệnh `cmake --build .` trong các phiên bản CMake gần đây.

## Đặt tùy chọn

Bạn đặt tùy chọn trong CMake với `-D`. Bạn có thể xem danh sách các tùy chọn với `-L`, hoặc danh sách có trợ giúp dễ đọc với `-LH`. Nếu bạn không liệt kê thư mục nguồn/xây dựng, danh sách sẽ không chạy lại CMake (`cmake -L` thay vì `cmake -L .`).

## Xây dựng chi tiết và một phần

Mặc dù không phải tất cả các công cụ xây dựng đều hỗ trợ, bạn có thể có các bản build chi tiết (chọn một):

```bash
~/package $ cmake --build build --verbose # Chỉ CMake 3.14+
~/package/build $ VERBOSE=1 make
```

Bạn thực sự có thể viết `make VERBOSE=1`, và make cũng sẽ làm đúng, mặc dù đó là một tính năng của `make` và không phải là dòng lệnh nói chung.

Bạn cũng có thể chỉ xây dựng một phần của build bằng cách chỉ định một mục tiêu, chẳng hạn như tên của thư viện hoặc tệp thực thi mà bạn đã định nghĩa trong CMake, và make sẽ chỉ xây dựng mục tiêu đó.
## Tùy chọn

CMake hỗ trợ các tùy chọn được lưu trữ. Một biến trong CMake có thể được đánh dấu là "được lưu trữ", nghĩa là nó sẽ được ghi vào bộ nhớ đệm (một tệp gọi là `CMakeCache.txt` trong thư mục build) khi nó được gặp. Bạn có thể đặt trước (hoặc thay đổi) giá trị của một tùy chọn được lưu trữ trên dòng lệnh với `-D`. Khi CMake tìm kiếm một biến được lưu trữ, nó sẽ sử dụng giá trị hiện có và sẽ không ghi đè lên nó.

### Tùy chọn tiêu chuẩn

Đây là các tùy chọn CMake phổ biến cho hầu hết các gói:

- `-DCMAKE_BUILD_TYPE=` Chọn từ Release, RelWithDebInfo, Debug, hoặc đôi khi nhiều hơn.
- `-DCMAKE_INSTALL_PREFIX=` Vị trí để cài đặt. Cài đặt hệ thống trên UNIX thường là `/usr/local` (mặc định), thư mục người dùng thường là `~/.local`, hoặc bạn có thể chọn một thư mục khác.
- `-DBUILD_SHARED_LIBS=` Bạn có thể đặt giá trị này là `ON` hoặc `OFF` để kiểm soát mặc định cho các thư viện chia sẻ (tác giả có thể chọn một trong hai thay vì sử dụng mặc định, tuy nhiên)
- `-DBUILD_TESTING=` Đây là tên phổ biến để bật kiểm tra, không phải tất cả các gói đều sử dụng nó, mặc dù đôi khi có lý do chính đáng.

## Gỡ lỗi các tệp CMake của bạn

Chúng tôi đã đề cập đến đầu ra chi tiết cho quá trình build, nhưng bạn cũng có thể xem đầu ra chi tiết khi cấu hình CMake. Tùy chọn `--trace` sẽ in ra từng dòng của CMake được chạy. Vì điều này rất chi tiết, CMake 3.7 đã thêm `--trace-source="tên tệp"`, sẽ in ra từng dòng được thực thi của chỉ tệp bạn quan tâm khi nó chạy. Nếu bạn chọn tên của tệp bạn quan tâm để gỡ lỗi (thường bằng cách chọn thư mục cha khi gỡ lỗi một CMakeLists.txt, vì tất cả chúng đều có cùng tên), bạn có thể chỉ xem các dòng chạy trong tệp đó. Rất hữu ích!
