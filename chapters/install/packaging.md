# Đóng gói

Có hai cách để hướng dẫn CMake xây dựng gói của bạn; một là sử dụng tệp CPackConfig.cmake và cách khác là tích hợp các biến CPack vào tệp CMakeLists.txt của bạn. Vì bạn muốn các biến từ bản dựng chính của mình được bao gồm, chẳng hạn như số phiên bản, bạn sẽ muốn tạo một tệp cấu hình nếu bạn đi theo con đường đó. Tôi sẽ chỉ cho bạn phiên bản tích hợp:

```cmake
# Hỗ trợ đóng gói
set(CPACK_PACKAGE_VENDOR "Tên nhà cung cấp")
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Mô tả tóm tắt")
set(CPACK_PACKAGE_VERSION_MAJOR ${PROJECT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR ${PROJECT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH ${PROJECT_VERSION_PATCH})
set(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/LICENSE")
set(CPACK_RESOURCE_FILE_README "${CMAKE_CURRENT_SOURCE_DIR}/README.md")
```

Đây là những biến phổ biến nhất mà bạn sẽ cần để tạo một gói nhị phân. Gói nhị phân sử dụng cơ chế cài đặt của CMake, vì vậy bất cứ thứ gì được cài đặt sẽ có mặt.

Bạn cũng có thể tạo một gói nguồn. Bạn nên đặt `CPACK_SOURCE_IGNORE_FILES` thành các biểu thức chính quy đảm bảo rằng bạn không chọn bất kỳ tệp bổ sung nào (như thư mục bản dựng hoặc chi tiết git); nếu không, `make package_source` sẽ đóng gói tất cả mọi thứ trong thư mục nguồn. Bạn cũng có thể đặt trình tạo nguồn để tạo các loại tệp ưa thích của bạn cho các gói nguồn:

```cmake
set(CPACK_SOURCE_GENERATOR "TGZ;ZIP")
set(CPACK_SOURCE_IGNORE_FILES
    /.git
    /dist
    /.*build.*
    /\\\\.DS_Store
)
```

Lưu ý rằng điều này sẽ không hoạt động trên Windows, nhưng các gói nguồn được tạo hoạt động trên Windows.

Cuối cùng, bạn cần bao gồm mô-đun CPack:

```cmake
include(CPack)
```