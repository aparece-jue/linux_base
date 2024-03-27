# 安装软件程序

- [安装软件程序](#安装软件程序)
  - [包管理基础](#包管理基础)
  - [基于Debian的系统](#基于debian的系统)
    - [用aptitude管理软件包](#用aptitude管理软件包)
    - [用aptitude安装软件包](#用aptitude安装软件包)
    - [用aptitude更新软件](#用aptitude更新软件)
    - [用aptitude卸载软件](#用aptitude卸载软件)
    - [aptitude仓库](#aptitude仓库)
  - [基于Red Hat的系统](#基于red-hat的系统)
    - [列出已安装包](#列出已安装包)
    - [yum安装软件](#yum安装软件)
    - [yum更新软件](#yum更新软件)
    - [yum卸载软件](#yum卸载软件)
    - [处理损坏的包的依赖关系](#处理损坏的包的依赖关系)
    - [yum软件仓库](#yum软件仓库)
  - [从源码安装](#从源码安装)

## 包管理基础

- 包管理系统(PMS)利用一个数据库来记录相关内容
  - 已安装的软件包
  - 每个包安装的文件
  - 每个已安装软件包的版本
- 软件包在服务器上，可利用本地linux上的PMS工具通过网络访问。这些服务器成为**仓库**。可用PMS工具搜索新的软件包，或更新软件包。
- 软件包通常会依赖其他包，为了前者能正常运行，被依赖的包必须提前安装在系统中。PMS会检测这些依赖关系，并在安装前先安装所有额外的依赖包。
- PMS没有统一的工具。linux中广泛使用的两种主要的PMS基础工具是dpkg和rpm。
  - 基于Deibian使用的是dpkg命令，dpkg会直接和linux系统的PMS交互，用来安装，管理和删除软件包。
  - 基于Red Hat使用的rpm命令，类似于dpkg,能够列出已安装包，安装新包和删除已有软件。

## 基于Debian的系统

- 这系列PMS的其他工具
  - dpkg
  - apt-get
  - apt-cache
  - aptitude
- aptitude工具本质上是apt工具和dpkg的前端，dpkg是软件包管理系统工具，aptitude是完整的软件包管理系统。

### 用aptitude管理软件包

- 直接运行会进入aptitude的全屏模式。
- 查看特定包的详情aptitude show package_name，输出的软件包相关详细信息来自于软件仓库。无法查看所有跟某个特定软件包相关的所有文件的列表。
- 可使用dpkg -L package_name 显示该软件包所安装的全部文件
- 可使用dpkg --search file_path，可以查看特定文件属于哪个软件包，必须使用绝对路径

### 用aptitude安装软件包

- 使用aptitude search package_name会自动搜索相关软件包，会隐式添加通配符。
- 第一列显示包的状态。
  - i (安装): 表示软件包已经被安装在系统中。
  - c (删除): 表示软件包已被删除，但配置文件仍然存在。
  - p (虚拟包): 表示该包是一个虚拟包（meta package），实际上并没有真正的软件包与之对应，而是作为其他软件包的依赖关系存在。，删除软件后显示p为软件包和配置数据文件一起删除了。
  - $ (手动安装): 表示该软件包是由用户手动安装的，而不是由其他软件包所依赖的。
- 找到软件包名后，可使用aptitude install package_name安装，会自动解析必要的包依赖，并安装需要的额外的库和软件。

### 用aptitude更新软件

- 使用aptitude safe-upgrade会将所有已安装的包更新到软件仓库的最新版本，有利于系统稳定。
- aptitude full-upgrade和aptitude dist-upgrade也会将所有软件包更新到最新版本，但不会检查包与包之间的依赖关系。

### 用aptitude卸载软件

- aptitude remove,只删除软件包，而不删除数据和配置文件。
- aptitude purge,删除软件包以及相关的数据和配置文件。

### aptitude仓库

- aptitude的默认仓库存储在/etc/apt/sources.list中。
- aptitude只会从这些仓库中下载文件，搜索软件进行安装或更新时，同样只会检查这些库。
- 仓库源的结构
  - 软件包类型，deb表示已编译程序源，deb-src表示源代码的源。
  - 软件仓库的地址
  - 特定软件仓库发行版版本的名称
  - 仓库中含有什么类型的包如main,restricted,universe,partner等。

## 基于Red Hat的系统

- 主要的前端工具
  - yum
  - urpm
  - zypper

### 列出已安装包

- yum list installed，列出已安装的包,更详细
- rpm -qa，只列出包名
- zypper search -I
- urpmq -i package_name 查看包信息
- rpm -q package_name 查看是否安装
- yum provides file_name 查看某个文件属于什么软件包

### yum安装软件

- yum install package_name
- yum localinstall package_name本地安装
- urpmi package_name
- zypper package_name

### yum更新软件

- yum list updates列出所有已安装的软件包的更新
- yum update package_name更新指定软件包
- urpm更新软件仓库时会自动更新，同时更新软件包
- urpmi --auto-update --update
- zypper update

### yum卸载软件

- yum remove package_name 只删除软件包并保留配置文件和数据文件
- yum erase package_name 删除软件包并删除配置文件和数据文件
- urpme package_name
- zypper remove package_name

### 处理损坏的包的依赖关系

- 安装软件时，某个包的软件依赖关系将另一个包的安装覆盖，称为损坏的包依赖关系。
- yum clean all，然后yum update,清理放错位置的文件。
- yum deplist package_name显示所有包的库依赖关系以及什么软件可以提供这些库依赖关系
- yum update --skip-broken 忽略依赖关系损坏的包，继续去更新其他软件包。
- urpmi -clean 如果clean选项不工作可以跳过依赖关系损坏的包
- zypper verify 验证和修复损坏的依赖关系

### yum软件仓库

- yum repolist 输出库来源
- 仓库的定义文件 /etc/yum.repos.d
- urpmq --list-media 显示仓库
- urpmi.addmedia path_name 添加仓库
- zypper repos 显示仓库
- zypper addrepo path_name 添加仓库

## 从源码安装

1. 下载文件源码
2. 使用tar解包
3. 按照README.md安装
4. 使用make构建各种二进制文件，make命令会编译源码，然后链接器会为这个包构建最终的可执行文件，make install是将已经编译好的程序或库安装到系统中的特定位置，使得它可以被系统中的其他程序调用和使用。
