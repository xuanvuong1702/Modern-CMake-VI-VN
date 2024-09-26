# Cài đặt CMake

:::{tip}

Phiên bản CMake của bạn phải mới hơn trình biên dịch của bạn. Nó phải mới hơn các thư viện bạn đang sử dụng. Các phiên bản mới hoạt động tốt hơn cho mọi người.

:::

Nếu bạn có một bản sao CMake được tích hợp sẵn, nó không đặc biệt hoặc được tùy chỉnh cho hệ thống của bạn. Bạn có thể dễ dàng cài đặt một bản mới thay thế, ở cấp hệ thống hoặc cấp người dùng. Hãy thoải mái hướng dẫn người dùng của bạn tại đây nếu họ phàn nàn về yêu cầu CMake được đặt quá cao. Đặc biệt nếu họ muốn hỗ trợ CMake 3.15 trở lên. Thậm chí nếu họ muốn hỗ trợ CMake 3.30 trở lên...

## Danh sách nhanh (thông tin thêm về mỗi phương pháp bên dưới)

Được sắp xếp theo sở thích của tác giả:

- Tất cả
  - [Pip(x)][pypi] (chính thức, thường cập nhật trong cùng ngày)
  - [Anaconda][] / [Conda-Forge][]
- Windows
  - [Winget][]
  - [Chocolatey][]
  - [Scoop][]
  - [MSYS2][]
  - [Tải xuống bản nhị phân][download] (chính thức)
- macOS
  - [Homebrew][]
  - [MacPorts][]
  - [Tải xuống bản nhị phân][download] (chính thức)
- Linux
  - [Snapcraft][snap] (chính thức)
  - [Kho APT][apt] (chỉ Ubuntu/Debian) (chính thức)
  - [Tải xuống bản nhị phân][download] (chính thức)

## Gói chính thức

Bạn có thể [tải xuống CMake từ KitWare][download]. Đây là cách bạn có thể sẽ nhận được CMake nếu bạn đang sử dụng Windows. Nó cũng không phải là một cách tồi để tải nó trên macOS (và phiên bản Universal2 được cung cấp hỗ trợ cả Intel và Apple Silicon), nhưng sử dụng `brew install cmake` sẽ tốt hơn nhiều nếu bạn sử dụng [Homebrew](https://brew.sh) (và bạn nên; Apple thậm chí còn hỗ trợ Homebrew chẳng hạn như trong quá trình triển khai Apple Silicon). Bạn cũng có thể tải nó trên hầu hết các trình quản lý gói khác, chẳng hạn như [Chocolatey](https://chocolatey.org) cho Windows hoặc [MacPorts](https://www.macports.org) cho macOS.

Trên Linux, có một số tùy chọn. Kitware cung cấp [kho lưu trữ apt Debian/Ubuntu][apt], cũng như [các gói snap][snap]. Có các bản nhị phân Linux phổ biến được cung cấp, nhưng bạn sẽ cần chọn một vị trí cài đặt. Nếu bạn đã sử dụng `~/.local` cho các gói không gian người dùng, lệnh một dòng sau[^1] sẽ tải CMake cho bạn[^2]:

```bash
~ $ wget -qO- "https://cmake.org/files/v3.30/cmake-3.30.2-linux-x86_64.tar.gz" | tar --strip-components=1 -xz -C ~/.local
```

Tên đã thay đổi trong CMake 3.20; các bản phát hành cũ hơn có tên như `cmake-3.19.7-Linux-x86_64.tar.gz`. Nếu bạn chỉ muốn một thư mục cục bộ chỉ có CMake:

```bash
~ $ mkdir -p cmake-3.30 && wget -qO- "https://cmake.org/files/v3.30/cmake-3.30.2-linux-x86_64.tar.gz" | tar --strip-components=1 -xz -C cmake-3.30
~ $ export PATH=`pwd`/cmake-3.30/bin:$PATH
```

Rõ ràng, bạn sẽ muốn nối vào PATH mỗi khi bạn bắt đầu một terminal mới hoặc thêm nó vào `.bashrc` của bạn hoặc vào hệ thống [LMod][].

Và, nếu bạn muốn cài đặt hệ thống, hãy cài đặt vào `/usr/local`; đây là một lựa chọn tuyệt vời trong container Docker, ví dụ: trên GitLab CI. Không thử nó trên hệ thống không phải container.

```bash
docker $ wget -qO- "https://cmake.org/files/v3.30/cmake-3.30.2-linux-x86_64.tar.gz" | tar --strip-components=1 -xz -C /usr/local
```

Nếu bạn đang ở trên hệ thống không có wget, hãy thay thế `wget -qO-` bằng `curl -s`.

Bạn cũng có thể build CMake trên bất kỳ hệ thống nào, nó khá dễ dàng, nhưng các bản nhị phân nhanh hơn.

## Phiên bản CMake mặc định

Dưới đây là một số môi trường build phổ biến và phiên bản CMake mà bạn sẽ tìm thấy trên chúng. Hãy thoải mái tự cài đặt CMake, nó chỉ mất 1-2 dòng và không có gì "đặc biệt" về phiên bản tích hợp sẵn. Nó cũng tương thích ngược rất tốt.

### Windows

[Gói winget][winget] là một cách tốt để tải CMake. Các tùy chọn khác:

[![Gói Chocolatey](https://repology.org/badge/version-for-repo/chocolatey/cmake.svg)][chocolatey]
[![Gói MSYS2 mingw](https://repology.org/badge/version-for-repo/msys2_mingw/cmake.svg)][msys2]
[![Gói MSYS2 msys2](https://repology.org/badge/version-for-repo/msys2_msys2/cmake.svg)][msys2]

Ngoài ra [Scoop][scoop] thường được cập nhật. Các trình cài đặt thông thường từ CMake.org cũng phổ biến trên Windows.

### macOS

[![Gói Homebrew](https://repology.org/badge/version-for-repo/homebrew/cmake.svg)][homebrew]
[![Gói Homebrew Casks](https://repology.org/badge/version-for-repo/homebrew_casks/cmake.svg)][homebrew-cask]
[![Gói MacPorts](https://repology.org/badge/version-for-repo/macports/cmake.svg)][macports]

Homebrew ngày nay phổ biến hơn nhiều trên macOS, ít nhất là theo Google Trends.

### Linux

#### RHEL/CentOS

[![Gói CentOS 7](https://repology.org/badge/version-for-repo/centos_7/cmake.svg?minversion=3.15.0)][centos]
[![Gói CentOS 8](https://repology.org/badge/version-for-repo/centos_8/cmake.svg?minversion=3.15.0)][centos]
[![Gói EPEL 7](https://repology.org/badge/version-for-repo/epel_7/cmake.svg?minversion=3.15.0)][centos]

Mặc định trên CentOS 8 không quá tệ, nhưng bạn không nên sử dụng mặc định trên CentOS 7 sắp hết vòng đời. Hãy sử dụng gói EPEL thay thế.

#### Ubuntu

[![Gói Ubuntu 18.04](https://repology.org/badge/version-for-repo/ubuntu_18_04/cmake.svg?minversion=3.15.0)](https://launchpad.net/ubuntu/bionic/+source/cmake)
[![Gói Ubuntu 20.04](https://repology.org/badge/version-for-repo/ubuntu_20_04/cmake.svg?minversion=3.15.0)](https://launchpad.net/ubuntu/focal/+source/cmake)
[![Gói Ubuntu 22.04](https://repology.org/badge/version-for-repo/ubuntu_22_04/cmake.svg?minversion=3.15.0)](https://launchpad.net/ubuntu/jammy/+source/cmake)
[![Gói Ubuntu 24.04](https://repology.org/badge/version-for-repo/ubuntu_24_04/cmake.svg?minversion=3.15.0)](https://launchpad.net/ubuntu/noble/+source/cmake)

#### Debian

[![Gói Debian 10](https://repology.org/badge/version-for-repo/debian_10/cmake.svg)][repology]
[![Gói Debian 10 backports](https://repology.org/badge/version-for-repo/debian_10_backports/cmake.svg)][repology]
[![Gói Debian 11](https://repology.org/badge/version-for-repo/debian_11/cmake.svg)][repology]
[![Gói Debian 11 backports](https://repology.org/badge/version-for-repo/debian_11_backports/cmake.svg)][repology]
[![Gói Debian Unstable](https://repology.org/badge/version-for-repo/debian_unstable/cmake.svg)][repology]

#### Khác

[![Gói Alpine Linux 3.20](https://repology.org/badge/version-for-repo/alpine_3_20/cmake.svg)](https://pkgs.alpinelinux.org/packages?name=cmake&branch=v3.20)
[![Gói Arch](https://repology.org/badge/version-for-repo/arch/cmake.svg)][repology]
[![Gói Fedora 40](https://repology.org/badge/version-for-repo/fedora_40/cmake.svg)][repology]
[![Cổng FreeBSD](https://repology.org/badge/version-for-repo/freebsd/cmake.svg)][repology]
[![Cổng OpenBSD](https://repology.org/badge/version-for-repo/openbsd/cmake.svg)][repology]
[![Gói Gentoo](https://repology.org/badge/version-for-repo/gentoo/cmake.svg)][repology]
[![Gói openSUSE Tumbleweed](https://repology.org/badge/version-for-repo/opensuse_tumbleweed/cmake.svg)][repology]
[![Gói Homebrew](https://repology.org/badge/version-for-repo/homebrew/cmake.svg)][homebrew]

### Các công cụ chung

[![Gói ConanCenter](https://repology.org/badge/version-for-repo/conancenter/cmake.svg)][repology]
[![PyPI](https://img.shields.io/pypi/v/cmake)][pypi]
[![Conda-forge](https://img.shields.io/conda/vn/conda-forge/cmake.svg)][conda-forge]
[![Anaconda](https://anaconda.org/anaconda/cmake/badges/version.svg?style=flat)][anaconda]

Chỉ cần `pip install cmake` trên nhiều hệ thống. Thêm `--user` nếu bạn phải làm vậy (pip hiện đại sẽ làm điều này cho bạn nếu cần). Cách này hiện chưa cung cấp các wheel Universal2.

### CI

| Distribution                                                                                                                         | Phiên bản CMake | Ghi chú                                                        |
| ------------------------------------------------------------------------------------------------------------------------------------ | ------------- | ------------------------------------------------------------ |
| [Azure DevOps](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/hosted?view=azure-devops#use-a-microsoft-hosted-agent) | 3.30.0        | Được cập nhật thường xuyên                                     |
| [GitHub Actions 20.04](https://github.com/actions/virtual-environments/blob/main/images/linux/Ubuntu2004-Readme.md)                  | 3.30.0        | Sử dụng cùng runner với Azure DevOps                           |

Nếu bạn đang sử dụng GitHub Actions, hãy xem thêm action [jwlawson/actions-setup-cmake](https://github.com/marketplace/actions/actions-setup-cmake), có thể cài đặt phiên bản CMake mà bạn lựa chọn, ngay cả trong một docker action.

### Danh sách đầy đủ

Các phiên bản thấp hơn 3.15 được đánh dấu bằng màu đỏ đậm hơn.

[![Danh sách đầy đủ](https://repology.org/badge/vertical-allrepos/cmake.svg?columns=3&minversion=3.15.0)][repology]

Xem thêm [pkgs.org/download/cmake](https://pkgs.org/download/cmake).

## Pip

[Cách này][pypi] cũng được cung cấp dưới dạng một gói chính thức, được duy trì bởi các tác giả của CMake tại KitWare và một số thành viên PyPA, bao gồm cả tôi. Giờ đây, nó được hỗ trợ trên các kiến trúc đặc biệt, như PowerPC trên Linux và Apple Silicon trên macOS, và cả trên các hệ thống MUSL như Alpine. Nếu bạn có pip (trình cài đặt gói của Python), bạn có thể làm như sau:

```bash
pip install cmake
```

Và miễn là có bản nhị phân tồn tại cho hệ thống của bạn, bạn sẽ có thể sử dụng nó gần như ngay lập tức. Nếu bản nhị phân không tồn tại, nó sẽ cố gắng sử dụng gói `scikit-build` của KitWare để build và sẽ yêu cầu một bản sao CMake cũ hơn để build. Vì vậy, chỉ sử dụng hệ thống này nếu các bản nhị phân tồn tại, đó là phần lớn thời gian.

Điều này cũng có lợi ích là tôn trọng môi trường ảo hiện tại của bạn. Tuy nhiên, nó thực sự tỏa sáng khi được đặt trong tệp `pyproject.toml` - nó sẽ chỉ được cài đặt để build gói của bạn và sẽ không còn lại sau đó! Tuyệt vời.

Tất nhiên, điều này cũng hoạt động với pipx. Vì vậy, bạn thậm chí có thể sử dụng `pipx run cmake` để chạy CMake trong môi trường ảo dùng một lần, mà không cần bất kỳ thiết lập nào - và điều này hoạt động ngay lập tức trên GitHub Actions, vì `pipx` là trình quản lý gói được hỗ trợ ở đó!

:::{tip}

Cá nhân tôi, trên Linux, tôi đặt các phiên bản CMake trong các thư mục, như `/opt/cmake312` hoặc `~/opt/cmake312`, rồi thêm chúng vào [LMod][]. Xem [`envmodule_setup`][envmodule_setup] để được trợ giúp thiết lập hệ thống LMod trên macOS hoặc Linux. Phải mất một chút thời gian để tìm hiểu, nhưng đó là một cách tuyệt vời để quản lý các phiên bản gói và trình biên dịch.

[envmodule_setup]: https://github.com/CLIUtils/envmodule_setup

:::

[^1]: Tôi giả định điều này là hiển nhiên, nhưng bạn đang tải xuống và chạy mã, điều này khiến bạn gặp phải tấn công man-in-the-middle. Nếu bạn đang ở trong môi trường quan trọng, bạn nên tải xuống tệp và kiểm tra checksum. (Và, không, chỉ cần thực hiện việc này trong hai bước không làm cho bạn an toàn hơn, chỉ có checksum mới an toàn hơn).
[^2]: Nếu bạn không có `.local` trong thư mục chính của mình, bạn có thể dễ dàng bắt đầu. Chỉ cần tạo thư mục, sau đó thêm `export PATH="$HOME/.local/bin:$PATH"` vào tệp `.bashrc` hoặc `.bash_profile` hoặc `.profile` của bạn trong thư mục chính. Bây giờ bạn có thể cài đặt bất kỳ gói nào bạn build thành `-DCMAKE_INSTALL_PREFIX=~/.local` thay vì `/usr/local`!

[repology]: https://repology.org/project/cmake/versions
[lmod]: http://lmod.readthedocs.io/en/latest/
[apt]: https://apt.kitware.com/
[snap]: https://snapcraft.io/cmake
[pypi]: https://pypi.org/project/cmake/
[winget]: https://github.com/microsoft/winget-pkgs/tree/master/manifests/k/Kitware/CMake
[chocolatey]: https://chocolatey.org/packages/cmake
[scoop]: https://github.com/ScoopInstaller/Main/blob/master/bucket/cmake.json
[msys2]: https://packages.msys2.org/base/mingw-w64-cmake
[anaconda]: https://anaconda.org/anaconda/cmake
[conda-forge]: https://github.com/conda-forge/cmake-feedstock
[download]: https://cmake.org/download/
[homebrew]: https://formulae.brew.sh/formula/cmake
[homebrew-cask]: https://formulae.brew.sh/cask/cmake
[macports]: https://ports.macports.org/port/cmake/summary
[centos]: https://rpms.remirepo.net/rpmphp/zoom.php?rpm=cmake