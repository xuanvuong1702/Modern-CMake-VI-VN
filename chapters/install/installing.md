# Cài đặt

Các lệnh cài đặt sẽ khiến một tệp hoặc đích được "cài đặt" vào cây cài đặt khi bạn chạy `make install`. Lệnh cài đặt đích cơ bản của bạn trông như thế này:

```cmake
install(TARGETS MyLib
        EXPORT MyLibTargets
        LIBRARY DESTINATION lib
        ARCHIVE DESTINATION lib
        RUNTIME DESTINATION bin
        INCLUDES DESTINATION include
        )
```

Các đích khác nhau chỉ cần thiết nếu bạn có thư viện, thư viện tĩnh hoặc chương trình để cài đặt. Đích `includes` là đặc biệt; vì một đích không cài đặt `includes`. Nó chỉ đặt đích `includes` trên đích được xuất (thường đã được đặt bởi `target_include_directories`, vì vậy hãy kiểm tra tệp MyLibTargets và đảm bảo rằng bạn không bao gồm thư mục include hai lần nếu bạn muốn các tệp cmake sạch).

Thường thì nên cho CMake quyền truy cập vào phiên bản, để `find_package` có thể chỉ định phiên bản. Điều đó trông như thế này:

```cmake
include(CMakePackageConfigHelpers)
write_basic_package_version_file(
    MyLibConfigVersion.cmake
    VERSION ${PACKAGE_VERSION}
    COMPATIBILITY AnyNewerVersion
    )
```

Tiếp theo bạn có hai lựa chọn. Bạn cần tạo `MyLibConfig.cmake`, nhưng bạn có thể thực hiện bằng cách xuất trực tiếp các đích của mình vào đó hoặc bằng cách viết nó bằng tay, sau đó bao gồm tệp đích. Tùy chọn sau là những gì bạn sẽ cần nếu bạn có bất kỳ phụ thuộc nào, thậm chí chỉ là OpenMP, vì vậy tôi sẽ minh họa phương pháp đó.

Đầu tiên, hãy tạo một tệp đích cài đặt (rất giống với tệp bạn đã tạo trong thư mục bản dựng):

```cmake
install(EXPORT MyLibTargets
        FILE MyLibTargets.cmake
        NAMESPACE MyLib::
        DESTINATION lib/cmake/MyLib
         )
```

Tệp này sẽ lấy các đích bạn đã xuất và đặt chúng vào một tệp. Nếu bạn không có phụ thuộc, chỉ cần sử dụng `MyLibConfig.cmake` thay vì `MyLibTargets.cmake` tại đây. Sau đó, hãy viết một tệp `MyLibConfig.cmake` tùy chỉnh ở đâu đó trong cây nguồn của bạn. Nếu bạn muốn thu thập các biến thời gian cấu hình, bạn có thể sử dụng tệp `.in` và bạn sẽ muốn sử dụng cú pháp `@var@`. Nội dung trông như thế này:

```cmake
include(CMakeFindDependencyMacro)

# Thu thập các giá trị từ cấu hình (tùy chọn)
set(my-config-var @my-config-var@)

# Cú pháp tương tự như find_package
find_dependency(MYDEP REQUIRED)

# Bất kỳ thiết lập bổ sung nào

# Thêm tệp đích
include("${CMAKE_CURRENT_LIST_DIR}/MyLibTargets.cmake")
```

Bây giờ, bạn có thể sử dụng tệp cấu hình (nếu bạn đã sử dụng tệp `.in`) và sau đó cài đặt tệp kết quả.
Vì chúng ta đã tạo một tệp `ConfigVersion`, đây là một nơi tốt để cài đặt nó.

```cmake
configure_file(MyLibConfig.cmake.in MyLibConfig.cmake @ONLY)
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/MyLibConfig.cmake"
              "${CMAKE_CURRENT_BINARY_DIR}/MyLibConfigVersion.cmake"
        DESTINATION lib/cmake/MyLib
        )
```

Đó là nó! Giờ đây, sau khi bạn cài đặt một gói, sẽ có các tệp trong `lib/cmake/MyLib` mà CMake sẽ tìm kiếm (cụ thể là, `MyLibConfig.cmake` và `MyLibConfigVersion.cmake`) và tệp đích mà cấu hình sử dụng cũng sẽ ở đó.

Khi CMake tìm kiếm một gói, nó sẽ tìm trong tiền tố cài đặt hiện tại và một số vị trí tiêu chuẩn. Bạn cũng có thể thêm điều này vào đường dẫn tìm kiếm của mình theo cách thủ công, bao gồm `MyLib_PATH`, và CMake cung cấp cho người dùng đầu ra trợ giúp đẹp nếu không tìm thấy tệp cấu hình.

Mô-đun [CMakePackageConfigHelpers](https://cmake.org/cmake/help/latest/module/CMakePackageConfigHelpers.html) được đề cập ở trên có các công cụ bổ sung để giúp viết tệp `Config.cmake` có thể di chuyển được nhiều hơn.
Tham khảo tài liệu CMake về [configure_package_config_file](https://cmake.org/cmake/help/latest/module/CMakePackageConfigHelpers.html#command:configure_package_config_file) (được sử dụng thay vì `configure_file`) và chuỗi thay thế `@PACKAGE_INIT@` để nhận được

- một tập hợp các biến `PACKAGE_<var>` được xác định tự động (cho các phiên bản đường dẫn tương đối của `<var>`) và
- một lệnh `set_and_check()` thay thế cho `set()` để tự động kiểm tra sự tồn tại của đường dẫn.
