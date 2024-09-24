# C++11 trở lên

C++11 được CMake hỗ trợ. Thực sự là vậy. Chỉ là không phải trong CMake 2.8, bởi vì, đoán xem, C++11 không tồn tại vào năm 2009 khi phiên bản 2.8 được phát hành. Miễn là bạn đang sử dụng CMake 3.1 trở lên, bạn sẽ ổn thôi, có hai cách khác nhau để kích hoạt hỗ trợ. Và như bạn sẽ sớm thấy, có hỗ trợ tốt hơn nữa trong CMake 3.8 trở lên. Tôi sẽ bắt đầu với điều đó, vì đây là CMake hiện đại.

## CMake 3.8+: Các tính năng meta-compiler

Miễn là bạn có thể yêu cầu người dùng cài đặt CMake, bạn sẽ có quyền truy cập vào cách mới nhất để kích hoạt các tiêu chuẩn C++. Đây là cách mạnh mẽ nhất, với cú pháp đẹp nhất và hỗ trợ tốt nhất cho các tiêu chuẩn mới, và hoạt động mục tiêu tốt nhất để kết hợp các tiêu chuẩn và tùy chọn. Giả sử bạn có một mục tiêu có tên là `myTarget`, nó trông như thế này:

```cmake
target_compile_features(myTarget PUBLIC cxx_std_11)
set_target_properties(myTarget PROPERTIES CXX_EXTENSIONS OFF)
```

Đối với dòng đầu tiên, chúng ta có thể chọn giữa `cxx_std_11`, `cxx_std_14` và `cxx_std_17`. Dòng thứ hai là tùy chọn, nhưng sẽ tránh việc thêm các phần mở rộng; nếu không có nó, bạn sẽ nhận được những thứ như `-std=g++11` thay thế `-std=c++11`. Dòng đầu tiên thậm chí còn hoạt động trên các mục tiêu `INTERFACE`; chỉ các mục tiêu được biên dịch thực tế mới có thể sử dụng dòng thứ hai.

Nếu một mục tiêu ở xa hơn trong chuỗi phụ thuộc chỉ định một cấp C++ cao hơn, điều này sẽ tương tác một cách mượt mà. Nó thực sự chỉ là một phiên bản nâng cao hơn của phương pháp sau đây, vì vậy nó cũng tương tác một cách mượt mà với điều đó.

## CMake 3.1+: Các tính năng trình biên dịch

Bạn có thể yêu cầu các tính năng trình biên dịch cụ thể phải có sẵn. Điều này chi tiết hơn so với việc yêu cầu một phiên bản C++, mặc dù hơi khó để chọn ra chính xác các tính năng mà một gói đang sử dụng trừ khi bạn đã viết gói đó và có một trí nhớ tốt. Vì điều này cuối cùng sẽ kiểm tra dựa trên danh sách các tùy chọn mà CMake biết trình biên dịch của bạn hỗ trợ và chọn cờ cao nhất được chỉ định trong danh sách đó, bạn không phải chỉ định tất cả các tùy chọn bạn cần, chỉ cần những tùy chọn hiếm nhất. Cú pháp giống hệt như phần trên, bạn chỉ cần có một danh sách các tùy chọn để chọn thay vì các tùy chọn `cxx_std_*`. Xem [toàn bộ danh sách tại đây](https://cmake.org/cmake/help/latest/prop_gbl/CMAKE_CXX_KNOWN_FEATURES.html).

Nếu bạn có các tính năng tùy chọn, bạn có thể sử dụng danh sách `CMAKE_CXX_COMPILE_FEATURES` và sử dụng `if(... IN_LIST ...)` từ CMake 3.3+ để xem tính năng đó có được hỗ trợ hay không và thêm nó có điều kiện. Xem [tài liệu tại đây](https://cmake.org/cmake/help/latest/manual/cmake-compile-features.7.html) để biết các trường hợp sử dụng khác.

## CMake 3.1+: Cài đặt toàn cục và thuộc tính

Có một cách khác mà các tiêu chuẩn C++ được hỗ trợ; một tập hợp cụ thể gồm ba thuộc tính (cả ở cấp độ toàn cục và mục tiêu). Các thuộc tính toàn cục là:

```cmake
set(CMAKE_CXX_STANDARD 11 CACHE STRING "Tiêu chuẩn C++ cần sử dụng")
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
```

Dòng đầu tiên thiết lập một cấp độ tiêu chuẩn C++, dòng thứ hai yêu cầu CMake sử dụng nó và dòng cuối cùng là tùy chọn và đảm bảo `-std=c++11` so với một cái gì đó như `-std=g++11`. Phương pháp này không tệ cho một gói cuối cùng, nhưng không nên được sử dụng bởi một thư viện. Bạn nên luôn thiết lập điều này như một biến được lưu trong bộ nhớ đệm, để bạn có thể dễ dàng ghi đè nó để thử một phiên bản mới (hoặc nếu điều này được sử dụng như một thư viện, đây là cách duy nhất để ghi đè nó - nhưng một lần nữa, đừng sử dụng điều này cho thư viện). Bạn cũng có thể thiết lập các giá trị này trên một mục tiêu:

```cmake
set_target_properties(myTarget PROPERTIES
    CXX_STANDARD 11
    CXX_STANDARD_REQUIRED YES
    CXX_EXTENSIONS NO
)
```

Điều này tốt hơn, nhưng vẫn không có kiểu kiểm soát rõ ràng mà các tính năng trình biên dịch có để điền vào các thuộc tính `PRIVATE` và `INTERFACE`, vì vậy nó thực sự chỉ hữu ích trên các mục tiêu cuối cùng.

Bạn có thể tìm thêm thông tin về hai phương pháp cuối cùng trên [bài đăng trên blog hữu ích của Craig Scott][crascit].

:::{danger}

Đừng tự thiết lập các cờ biên dịch thủ công. Sau đó, bạn sẽ phải chịu trách nhiệm duy trì các cờ chính xác cho mọi bản phát hành của mọi trình biên dịch, thông báo lỗi cho các trình biên dịch không được hỗ trợ sẽ không hữu ích và một số IDE có thể không nhận các cờ thủ công.

:::

[crascit]: https://crascit.com/2015/03/28/enabling-cxx11-in-cmake/
