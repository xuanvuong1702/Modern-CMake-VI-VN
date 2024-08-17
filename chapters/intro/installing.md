# Cài đặt CMake

:::{tip}

Phiên bản CMake của bạn nên mới hơn trình biên dịch của bạn. Nó nên mới hơn các thư viện bạn đang sử dụng. Các phiên bản mới hơn hoạt động tốt hơn cho tất cả mọi người.

:::

Nếu bạn có một bản sao CMake tích hợp sẵn, nó không phải là đặc biệt hay tùy chỉnh cho hệ thống của bạn. Bạn có thể dễ dàng cài đặt một bản mới thay thế, hoặc ở cấp hệ thống hoặc cấp người dùng. Hãy hướng dẫn người dùng của bạn ở đây nếu họ phàn nàn về yêu cầu CMake quá cao. Đặc biệt nếu họ muốn hỗ trợ 3.15+ hoặc thậm chí 3.30+...

## Danh sách nhanh (thông tin chi tiết về từng phương pháp bên dưới)

Được sắp xếp theo thứ tự ưu tiên của tác giả:

- Tất cả
  - [Pip(x)][pypi] (chính thức, thường cập nhật cùng ngày)
  - [Anaconda][] / [Conda-Forge][]
- Windows
  - [Winget][]
  - [Chocolatey][]
  - [Scoop][]
  - [MSYS2][]
  - [Tải xuống bản nhị phân][download] (chính thức)
- MacOS
  - [Homebrew][]
  - [MacPorts][]
  - [Tải xuống bản nhị phân][download] (chính thức)
- Linux
  - [Snapcraft][snap] (chính thức)
  - [Kho APT][apt] (chỉ Ubuntu/Debian) (chính thức)
  - [Tải xuống bản nhị phân][download] (chính thức)

## Gói chính thức

Bạn có thể [tải CMake từ KitWare][download]. Đây có lẽ là cách bạn sẽ lấy CMake nếu bạn đang sử dụng Windows. Đây cũng không phải là cách tồi để lấy nó trên macOS (và một phiên bản Universal2 hỗ trợ cả Intel và Apple Silicon được cung cấp), nhưng sử dụng `brew install cmake` sẽ tiện lợi hơn nếu bạn dùng [Homebrew](https://brew.sh) (và bạn nên dùng; Apple thậm chí còn hỗ trợ Homebrew trong quá trình triển khai Apple Silicon). Bạn cũng có thể lấy nó trên hầu hết các trình quản lý gói khác, như [Chocolatey](https://chocolatey.org) cho Windows hoặc [MacPorts](https://www.macports.org) cho macOS.

Trên Linux, có nhiều lựa chọn. Kitware cung cấp một [kho apt cho Debian/Ubuntu][apt], cũng như [gói snap][snap]. Có các tệp nhị phân Linux phổ quát được cung cấp, nhưng bạn sẽ cần chọn một vị trí cài đặt. Nếu bạn đã sử dụng `~/.local` cho các gói người dùng, lệnh đơn dòng sau[^1] sẽ giúp bạn lấy CMake [^2]:

```bash
~ $ wget -qO- "https://cmake.org/files/v3.30/cmake-3.30.2-linux-x86_64.tar.gz" | tar --strip-components=1 -xz -C ~/.local
```

Tên đã thay đổi trong phiên bản 3.20; các phiên bản cũ hơn có tên như `cmake-3.19.7-Linux-x86_64.tar.gz`. Nếu bạn chỉ muốn một thư mục cục bộ chỉ chứa CMake:

```bash
~ $ mkdir -p cmake-3.30 && wget -qO- "https://cmake.org/files/v3.30/cmake-3.30.2-linux-x86_64.tar.gz" | tar --strip-components=1 -xz -C cmake-3.30
~ $ export PATH=`pwd`/cmake-3.30/bin:$PATH
```

Rõ ràng là bạn sẽ muốn thêm vào PATH mỗi khi bạn mở một terminal mới, hoặc thêm nó vào `.bashrc` của bạn hoặc vào hệ thống [LMod][].

Và, nếu bạn muốn cài đặt hệ thống, cài đặt vào `/usr/local`; đây là một lựa chọn tuyệt vời trong một container Docker, ví dụ như trên GitLab CI. Đừng thử nó trên một hệ thống không được container hóa.

```bash
docker $ wget -qO- "https://cmake.org/files/v3.30/cmake-3.30.2-linux-x86_64.tar.gz" | tar --strip-components=1 -xz -C /usr/local
```

Nếu bạn đang ở trên một hệ thống không có wget, thay thế `wget -qO-` bằng `curl -s`.

Bạn cũng có thể build CMake trên bất kỳ hệ thống nào, điều này khá dễ dàng, nhưng các tệp nhị phân sẽ nhanh hơn.

## Phiên bản CMake Mặc Định

Dưới đây là một số môi trường build phổ biến và phiên bản CMake mà bạn sẽ tìm thấy trên chúng. Bạn có thể tự cài đặt CMake, chỉ cần 1-2 dòng lệnh và không có gì "đặc biệt" về phiên bản tích hợp sẵn. CMake cũng rất tương thích ngược.

### Windows

[Gói winget][winget] là một cách tốt để cài đặt CMake. Các tùy chọn khác:

[![Gói Chocolatey](https://repology.org/badge/version-for-repo/chocolatey/cmake.svg)][chocolatey]
[![Gói MSYS2 mingw](https://repology.org/badge/version-for-repo/msys2_mingw/cmake.svg)][msys2]
[![Gói MSYS2 msys2](https://repology.org/badge/version-for-repo/msys2_msys2/cmake.svg)][msys2]

Ngoài ra, [Scoop][scoop] thường xuyên được cập nhật. Các trình cài đặt thông thường từ CMake.org cũng rất phổ biến trên Windows.

### MacOS

[![Gói Homebrew](https://repology.org/badge/version-for-repo/homebrew/cmake.svg)][homebrew]
[![Gói Homebrew Casks](https://repology.org/badge/version-for-repo/homebrew_casks/cmake.svg)][homebrew-cask]
[![Gói MacPorts](https://repology.org/badge/version-for-repo/macports/cmake.svg)][macports]

Homebrew hiện nay khá phổ biến trên macOS, ít nhất là theo Google Trends.

### Linux

#### RHEL/CentOS

[![CentOS 7 package](https://repology.org/badge/version-for-repo/centos_7/cmake.svg?minversion=3.15.0)][centos]
[![CentOS 8 package](https://repology.org/badge/version-for-repo/centos_8/cmake.svg?minversion=3.15.0)][centos]
[![EPEL 7 package](https://repology.org/badge/version-for-repo/epel_7/cmake.svg?minversion=3.15.0)][centos]

Phiên bản mặc định trên CentOS 8 không quá tệ, nhưng bạn không nên sử dụng phiên bản mặc định trên CentOS 7 đã hết vòng đời. Thay vào đó, hãy sử dụng gói EPEL.

#### Ubuntu

[![Ubuntu 18.04 package](https://repology.org/badge/version-for-repo/ubuntu_18_04/cmake.svg?minversion=3.15.0)](https://launchpad.net/ubuntu/bionic/+source/cmake)
[![Ubuntu 20.04 package](https://repology.org/badge/version-for-repo/ubuntu_20_04/cmake.svg?minversion=3.15.0)](https://launchpad.net/ubuntu/focal/+source/cmake)
[![Ubuntu 22.04 package](https://repology.org/badge/version-for-repo/ubuntu_22_04/cmake.svg?minversion=3.15.0)](https://launchpad.net/ubuntu/jammy/+source/cmake)
[![Ubuntu 24.04 package](https://repology.org/badge/version-for-repo/ubuntu_24_04/cmake.svg?minversion=3.15.0)](https://launchpad.net/ubuntu/noble/+source/cmake)

#### Debian

[![Debian 10 package](https://repology.org/badge/version-for-repo/debian_10/cmake.svg)][repology]
[![Debian 10 backports package](https://repology.org/badge/version-for-repo/debian_10_backports/cmake.svg)][repology]
[![Debian 11 package](https://repology.org/badge/version-for-repo/debian_11/cmake.svg)][repology]
[![Debian 11 backports package](https://repology.org/badge/version-for-repo/debian_11_backports/cmake.svg)][repology]
[![Debian Unstable package](https://repology.org/badge/version-for-repo/debian_unstable/cmake.svg)][repology]

#### Other

[![Alpine Linux 3.20 package](https://repology.org/badge/version-for-repo/alpine_3_20/cmake.svg)](https://pkgs.alpinelinux.org/packages?name=cmake&branch=v3.20)
[![Arch package](https://repology.org/badge/version-for-repo/arch/cmake.svg)][repology]
[![Fedora 40 package](https://repology.org/badge/version-for-repo/fedora_40/cmake.svg)][repology]
[![FreeBSD port](https://repology.org/badge/version-for-repo/freebsd/cmake.svg)][repology]
[![OpenBSD port](https://repology.org/badge/version-for-repo/openbsd/cmake.svg)][repology]
[![Gentoo package](https://repology.org/badge/version-for-repo/gentoo/cmake.svg)][repology]
[![openSUSE Tumbleweed package](https://repology.org/badge/version-for-repo/opensuse_tumbleweed/cmake.svg)][repology]
[![Homebrew package](https://repology.org/badge/version-for-repo/homebrew/cmake.svg)][homebrew]

### General tools

[![ConanCenter package](https://repology.org/badge/version-for-repo/conancenter/cmake.svg)][repology]
[![PyPI](https://img.shields.io/pypi/v/cmake)][pypi]
[![Conda-forge](https://img.shields.io/conda/vn/conda-forge/cmake.svg)][conda-forge]
[![Anaconda](https://anaconda.org/anaconda/cmake/badges/version.svg?style=flat)][anaconda]
Chỉ cần `pip install cmake` trên nhiều hệ thống. Thêm `--user` nếu cần (pip hiện đại sẽ tự động làm điều này nếu cần). Hiện tại chưa cung cấp Universal2 wheels.

### CI

| Phân phối                                                                                                                            | Phiên bản CMake | Ghi chú                                                      |
| ------------------------------------------------------------------------------------------------------------------------------------ | --------------- | ------------------------------------------------------------ |
| [Azure DevOps](https://docs.microsoft.com/en-us/azure/devops/pipelines/agents/hosted?view=azure-devops#use-a-microsoft-hosted-agent) | 3.30.0          | luôn được cập nhật                                           |
| [GitHub Actions 20.04](https://github.com/actions/virtual-environments/blob/main/images/linux/Ubuntu2004-Readme.md)                  | 3.30.0          | cùng runners như Azure DevOps                                |

Nếu bạn đang sử dụng GitHub Actions, hãy xem thêm action [jwlawson/actions-setup-cmake](https://github.com/marketplace/actions/actions-setup-cmake), có thể cài đặt phiên bản CMake bạn chọn, ngay cả trong một docker action run.

### Danh sách đầy đủ

Các phiên bản dưới 3.15 được đánh dấu bằng màu đỏ đậm hơn.

[![Danh sách đầy đủ](https://repology.org/badge/vertical-allrepos/cmake.svg?columns=3&minversion=3.15.0)][repology]

Xem thêm tại [pkgs.org/download/cmake](https://pkgs.org/download/cmake).

## Pip

[Đây][pypi] cũng được cung cấp như một gói chính thức, được duy trì bởi các tác giả của CMake tại KitWare và một số thành viên của PyPA, bao gồm cả tôi. Hiện tại nó được hỗ trợ trên các kiến trúc đặc biệt, như PowerPC trên Linux và Apple Silicon trên macOS, và trên các hệ thống MUSL như Alpine. Nếu bạn có pip (trình cài đặt gói của Python), bạn có thể làm:

```bash
pip install cmake
```
Miễn là có tệp nhị phân cho hệ thống của bạn, bạn sẽ có thể chạy ngay lập tức. Nếu không có tệp nhị phân, nó sẽ cố gắng sử dụng gói `scikit-build` của KitWare để xây dựng, và sẽ yêu cầu một bản sao cũ hơn của CMake để xây dựng. Vì vậy, chỉ sử dụng hệ thống này nếu có tệp nhị phân, điều này xảy ra hầu hết thời gian.

Điều này cũng có lợi ích là tôn trọng môi trường ảo hiện tại của bạn. Nó thực sự tỏa sáng khi được đặt trong tệp `pyproject.toml` - nó sẽ chỉ được cài đặt để xây dựng gói của bạn, và sẽ không còn lại sau đó! Thật tuyệt vời.

Điều này cũng hoạt động với pipx. Vì vậy, bạn thậm chí có thể sử dụng `pipx run cmake` để chạy CMake trong một môi trường ảo dùng một lần, mà không cần bất kỳ thiết lập nào - và điều này hoạt động ngay lập tức trên GitHub Actions, vì `pipx` là một trình quản lý gói được hỗ trợ ở đó!

:::{tip}

Cá nhân tôi, trên Linux, tôi đặt các phiên bản của CMake trong các thư mục, như `/opt/cmake312` hoặc `~/opt/cmake312`, và sau đó thêm chúng vào [LMod][]. Xem [`envmodule_setup`][envmodule_setup] để được trợ giúp thiết lập hệ thống LMod trên macOS hoặc Linux. Nó mất một chút thời gian để học, nhưng là một cách tuyệt vời để quản lý các phiên bản gói và trình biên dịch.

[envmodule_setup]: https://github.com/CLIUtils/envmodule_setup

:::

[^1]: Tôi cho rằng điều này là hiển nhiên, nhưng bạn đang tải xuống và chạy mã, điều này khiến bạn dễ bị tấn công man-in-the-middle. Nếu bạn đang ở trong một môi trường quan trọng, bạn nên tải xuống tệp và kiểm tra checksum. (Và, không, chỉ đơn giản làm điều này trong hai bước không làm bạn an toàn hơn, chỉ có kiểm tra checksum mới an toàn hơn).
[^2]: Nếu bạn không có `.local` trong thư mục chính của mình, rất dễ để bắt đầu. Chỉ cần tạo thư mục, sau đó thêm `export PATH="$HOME/.local/bin:$PATH"` vào tệp `.bashrc` hoặc `.bash_profile` hoặc `.profile` trong thư mục chính của bạn. Bây giờ bạn có thể cài đặt bất kỳ gói nào bạn xây dựng vào `-DCMAKE_INSTALL_PREFIX=~/.local` thay vì `/usr/local`!


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
