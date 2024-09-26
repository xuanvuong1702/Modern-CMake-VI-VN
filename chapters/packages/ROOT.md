## ROOT

ROOT là một bộ công cụ C++ dành cho Vật lý Năng lượng Cao. Nó rất lớn. Có rất nhiều cách để sử dụng nó trong CMake, mặc dù nhiều/hầu hết các ví dụ bạn tìm thấy có thể là sai. Đây là khuyến nghị của tôi.

Quan trọng nhất, có _rất nhiều cải tiến_ trong hỗ trợ CMake trong các phiên bản ROOT gần đây - Sử dụng 6.16+ dễ dàng hơn rất nhiều! Nếu bạn thực sự phải hỗ trợ 6.14 hoặc phiên bản trước đó, hãy xem phần cuối. Cũng có những cải tiến hơn nữa trong 6.20, nó hoạt động giống như một dự án CMake đúng đắn hơn, và xuất các tính năng tiêu chuẩn C++ cho các target, v.v.

## Tìm kiếm ROOT

ROOT 6.10+ hỗ trợ khám phá tệp cấu hình, vì vậy bạn có thể thực hiện:

```{literalinclude} ../../examples/root-simple/CMakeLists.txt
:start-after: "[find_package]"
:end-before: "[find_package]"
:language: cmake
```

để thử tìm ROOT. Nếu bạn chưa thiết lập đường dẫn, bạn có thể truyền `-DROOT_DIR=$ROOTSYS/cmake` để tìm ROOT. (Nhưng, thực sự, bạn nên source `thisroot.sh`).

## Cách đúng (Target)

ROOT 6.12 trở về trước không thêm thư mục include cho các target được import. ROOT 6.14+ đã sửa lỗi này và các thuộc tính target bắt buộc đã trở nên tốt hơn. Phương pháp này đang nhanh chóng trở nên dễ sử dụng hơn (xem ví dụ ở cuối trang này để biết chi tiết về ROOT cũ hơn).

Để liên kết, chỉ cần chọn các thư viện bạn muốn sử dụng:

```{literalinclude} ../../examples/root-simple/CMakeLists.txt
:start-after: "[add_and_link]"
:end-before: "[add_and_link]"
:language: cmake
```

Nếu bạn muốn xem danh sách mặc định, hãy chạy `root-config --libs` trên dòng lệnh. Trong Homebrew ROOT 6.18, đây sẽ là:

- `ROOT::Core`
- `ROOT::Gpad`
- `ROOT::Graf3d`
- `ROOT::Graf`
- `ROOT::Hist`
- `ROOT::Imt`
- `ROOT::MathCore`
- `ROOT::Matrix`
- `ROOT::MultiProc`
- `ROOT::Net`
- `ROOT::Physics`
- `ROOT::Postscript`
- `ROOT::RIO`
- `ROOT::ROOTDataFrame`
- `ROOT::ROOTVecOps`
- `ROOT::Rint`
- `ROOT::Thread`
- `ROOT::TreePlayer`
- `ROOT::Tree`

## Cách toàn cục cũ

ROOT [cung cấp một tiện ích](https://root.cern.ch/how/integrate-root-my-project-cmake) để thiết lập một dự án ROOT, mà bạn có thể kích hoạt bằng cách sử dụng `include("${ROOT_USE_FILE}")`. Điều này sẽ tự động tạo các biến cấp thư mục và toàn cục xấu xí cho bạn. Nó sẽ giúp bạn tiết kiệm một ít thời gian thiết lập và sẽ lãng phí rất nhiều thời gian sau này nếu bạn cố gắng làm bất cứ điều gì phức tạp. Miễn là bạn không tạo thư viện, nó có thể ổn cho các tập lệnh đơn giản. Include và cờ được đặt toàn cục, nhưng bạn vẫn cần tự liên kết với `${ROOT_LIBRARIES}`, cùng với có thể là `ROOT_EXE_LINKER_FLAGS` (Bạn sẽ phải `separate_arguments` trước khi liên kết hoặc bạn sẽ gặp lỗi nếu có nhiều cờ, như trên macOS). Ngoài ra, trước 6.16, bạn phải tự sửa lỗi về khoảng cách.

Đây là những gì nó sẽ trông như thế nào:

```{literalinclude} ../../examples/root-usefile/CMakeLists.txt
:start-after: "[core]"
:end-before: "[core]"
:language: cmake
```

## Thành phần

Find ROOT cho phép bạn chỉ định các thành phần. Nó sẽ thêm bất cứ thứ gì bạn liệt kê vào `${ROOT_LIBRARIES}`, vì vậy bạn có thể muốn xây dựng target của riêng mình bằng cách sử dụng điều đó để tránh liệt kê các thành phần hai lần. Điều này không giải quyết được các dependency (phụ thuộc); đó là một lỗi khi liệt kê `RooFit` nhưng không phải `RooFitCore`. Nếu bạn liên kết đến `ROOT::RooFit` thay vì `${ROOT_LIBRARIES}`, thì `RooFitCore` không bắt buộc.

## Tạo từ điển

Tạo từ điển là cách ROOT giải quyết tính năng reflection (phản chiếu) bị thiếu trong C++. Nó cho phép ROOT tìm hiểu chi tiết về lớp của bạn để nó có thể lưu nó, hiển thị các phương thức trong trình thông dịch Cling, v.v. Mã nguồn của bạn sẽ cần các sửa đổi sau để hỗ trợ tạo từ điển:

- Định nghĩa lớp của bạn nên kết thúc bằng `ClassDef(MyClassName, 1)`
- Triển khai lớp của bạn nên có `ClassImp(MyClassName)` trong đó

ROOT cung cấp các tệp nhị phân `rootcling` và `genreflex` (một giao diện kế thừa cho `rootcling`) tạo ra các tệp nguồn cần thiết để xây dựng từ điển. Nó cũng định nghĩa `root_generate_dictionary`, một hàm CMake để gọi `rootcling` trong quá trình build.

Để tải hàm này, trước tiên hãy include các macro ROOT:

```cmake
include("${ROOT_DIR}/modules/RootNewMacros.cmake")
# Đối với các phiên bản ROOT hơn 6.16, mọi thứ sẽ bị hỏng
# nếu không có gì trong danh sách include toàn cục!
if (${ROOT_VERSION} VERSION_LESS "6.16")
    include_directories(ROOT_NONEXISTENT_DIRECTORY_HACK)
endif()
```

Điều kiện `if(...)` được thêm vào để sửa lỗi trong tệp NewMacros khiến việc tạo từ điển không thành công nếu không có ít nhất một thư mục include toàn cục hoặc thư mục `inc`. Ở đây tôi đang include một thư mục không tồn tại chỉ để làm cho nó hoạt động. Không có thư mục `ROOT_NONEXISTENT_DIRECTORY_HACK`.

`rootcling` sử dụng một tệp tiêu đề đặc biệt với [công thức cụ thể][linkdef-root] để xác định phần nào để tạo từ điển. Tên của tệp này có thể có bất kỳ tiền tố nào, nhưng **phải** kết thúc bằng `LinkDef.h`. Sau khi bạn đã viết tệp tiêu đề này, có thể gọi hàm tạo từ điển.

### Tạo từ điển thủ công

Đôi khi, bạn có thể muốn yêu cầu ROOT tạo từ điển, sau đó tự thêm tệp nguồn vào target thư viện của mình. Bạn có thể gọi `root_generate_dictionary` với tên của từ điển, ví dụ: `G__Example`, bất kỳ tệp tiêu đề nào cần thiết và cuối cùng là tệp `LinkDef.h` đặc biệt, được liệt kê sau `LINKDEF`:

```cmake
root_generate_dictionary(G__Example Example.h LINKDEF ExampleLinkDef.h)
```

Lệnh này sẽ tạo ba tệp:

- `${NAME}.cxx`: Tệp này nên được bao gồm trong nguồn của bạn khi bạn tạo thư viện của mình.
- `lib{NAME}.rootmap` (tiền tố `G__` bị xóa): Tệp rootmap ở dạng văn bản thuần túy
- `lib{NAME}_rdict.pcm` (tiền tố `G__` bị xóa): Tệp [mô-đun được biên dịch trước của ROOT][]
  Tên (`${NAME}`) của target mà bạn phải tạo được xác định bởi tên từ điển; nếu tên từ điển bắt đầu bằng `G__`, nó sẽ bị xóa. Nếu không, tên được sử dụng trực tiếp.

Hai tệp đầu ra cuối cùng phải nằm cạnh đầu ra thư viện. Điều này được thực hiện bằng cách kiểm tra `CMAKE_LIBRARY_OUTPUT_DIRECTORY` (nó sẽ không chọn cài đặt target cục bộ). Nếu bạn đã đặt libdir nhưng bạn chưa đặt vị trí cài đặt (toàn cục), bạn cũng sẽ cần đặt `ARG_NOINSTALL` thành `TRUE`.

### Xây dựng từ điển với một target hiện có

Thay vì tự thêm các tệp được tạo vào nguồn thư viện của bạn, bạn có thể yêu cầu ROOT làm điều này cho bạn bằng cách chuyển đối số `MODULE`. Đối số này phải chỉ định tên của một target build hiện có:

```cmake
add_library(Example)
root_generate_dictionary(G__Example Example.h MODULE Example LINKDEF ExampleLinkDef.h)
```

Tên đầy đủ của từ điển (ví dụ: `G__Example`) không được giống hệt với đối số `MODULE`.

[linkdef-root]: https://root.cern.ch/selecting-dictionary-entries-linkdefh
[root pre-compiled module file]: https://inspirehep.net/literature/1413967

---

# Sử dụng ROOT cũ

Nếu bạn thực sự phải sử dụng ROOT cũ hơn, bạn sẽ cần một cái gì đó như thế này:

```cmake
# Target ROOT bị thiếu include và cờ trong ROOT 6.10 và 6.12
set_property(TARGET ROOT::Core PROPERTY
    INTERFACE_INCLUDE_DIRECTORIES "${ROOT_INCLUDE_DIRS}")

# ROOT ban đầu không bao gồm các cờ bắt buộc trên target
add_library(ROOT::Flags_CXX IMPORTED INTERFACE)


# ROOT 6.14 trở về trước có lỗi khoảng cách trong cờ trình liên kết
string(REPLACE "-L " "-L" ROOT_EXE_LINKER_FLAGS "${ROOT_EXE_LINKER_FLAGS}")

# Sửa lỗi cho ROOT_CXX_FLAGS không thực sự là danh sách CMake
separate_arguments(ROOT_CXX_FLAGS)
set_property(TARGET ROOT::Flags_CXX APPEND PROPERTY
    INTERFACE_COMPILE_OPTIONS ${ROOT_CXX_FLAGS})

# Thêm định nghĩa
separate_arguments(ROOT_DEFINITIONS)
foreach(_flag ${ROOT_EXE_LINKER_FLAG_LIST})
    # Xóa -D hoặc /D nếu có
    string(REGEX REPLACE [=[^[-//]D]=] "" _flag ${_flag})
    set_property(TARGET ROOT::Flags APPEND PROPERTY INTERFACE_LINK_LIBRARIES ${_flag})
endforeach()

# Điều này cũng sửa lỗi trong cờ trình liên kết
separate_arguments(ROOT_EXE_LINKER_FLAGS)
set_property(TARGET ROOT::Flags_CXX APPEND PROPERTY
    INTERFACE_LINK_LIBRARIES ${ROOT_EXE_LINKER_FLAGS})

# Đảm bảo bạn cũng liên kết với ROOT::Flags_CXX!
```
