# 软件安装：RPM、SRPM与YUM功能

## 软件管理器简介

### Linux界的两大主流：RPM与DPKG

|发行版             |RedHat/Fedora/CentOS/SuSE  |Debian/Ubuntu      |
|-------------------|---------------------------|-------------------|
|软件管理机制       |RPM                        |DPKG               |
|使用命令           |rpm, rpmbuild              |dpkg               |
|在线升级机制       |YUM(RedHat/Fedora/CentOS)<br />YOU(SuSE)|APT   |
|在线升级命令       |yum                        |apt-get            |

说明：
1. YUM = Yellow dog Updater, Modified
2. YOU = Yast Online Udate


### 什么是RPM与SRPM

RPM = RedHat Package Manager

RPM是以一种数据库记录的方式来将所需要的软件安装到Linux系统的一套管理机制。RPM里包含了编译好的软件和软件对应的依赖。
安装的时候会检查依赖是否满足，满足则安装，不满足则不安装。

RPM的优点：
1. RPM包中包含的是已经编译好的软件，无需再重新编译，方便软件传输和安装；
2. RPM方式安装软件后，软件的信息都会记录在Linux主机的数据库中，能够很方便地进行查询、升级和卸载。

RPM的缺点：
1. 不同发行商的RPM可能不兼容；如：RedHat的RPM文件就无法在SuSE系统上进行安装；
2. 相同发行商的不同版本之间也可能不兼容；如：CentOS 4的RPM文件就不能直接在CentOS 5上进行安装；
3. 由前两条可以看出，软件安装的环境必须要与打包时的环境一致或相当才行；
4. 安装时需要满足软件的依赖属性需求；
5. 卸载时要注意，不能先删除低层次的软件，否则会造成系统的问题。

SRPM = Source RPM

SRPM里面提供的是源代码，而不是编译好的软件。

对于SRPM，需要：
1. 先将SRPM编译成RPM文件；
2. 然后再将RPM安装到系统当中。

#### RPM vs SRPM

|文件格式           |RPM                        |SRPM               |
|-------------------|---------------------------|-------------------|
|文件名格式         |.rpm                       |.src.rpm           |
|是否可以直接安装   |可以                       |不可以             |
|内含程序类型       |已编译，二进制             |未编译，源码       |
|是否可以修改参数并编译|不可以                  |可以               |

补充说明：
1. CentOS是“社区维护的企业版”，因为其是社区根据RHEL发布的源码（SRPM）重新编译发布的产物；
2. 可以通过SRPM内部的编译参数来学习CentOS是如何编译一个程序的。


### 什么是i386、i586、i686、noarch、x86_64

示例：对于`rp-pppoe-3.1-5.i386.rpm`，分解说明如下：

|部分               |含义                       |
|-------------------|---------------------------|
|rp-pppoe           |软件名称                   |
|3.1                |版本信息                   |
|5                  |发布次数                   |
|i386               |硬件平台                   |
|rpm                |扩展名                     |

硬件平台`noarch`是指没有硬件等级上的限制，也就是说适用于任何平台。这种类型的RPM文件里面一般没有二进制程序，较常见的就是shell脚本程序。

说明：
1. 由于硬件的升级是向下兼容的，所以在老的硬件平台上开发的软件能够在新的硬件平台上运行；而在新的硬件平台上开发的软件就不能在就得硬件平台上运行。
2. 目前的主流硬件平台是x86_64的，所以在这个平台上可以安装x86_64的软件，也可以安装i386的软件。


### RPM属性依赖的解决方式：YUM在线升级

系统发行商在发布软件时，都会将软件的内容分为一般使用和开发使用两大类。分别对应的文件名为：`software.i386.rpm`和`software-devel.i386.rpm`。
系统默认安装的都是一般使用的软件，因为开发使用的软件大部分人都是不会用到的。

YUM的工作原理：
1. 发布的软件会放到YUM服务器上，YUM服务器会分析记录该软件的依赖软件列表以及对应的存放位置。将依赖软件列表以及对应的存放位置置于容器（Repo）当中。
2. 使用YUM安装、升级软件的时候，yum会向容器获取依赖软件列表，拿到列表后会与本地的RPM数据库进行比较，这样就知道实际需要下载安装、升级哪些软件了。
3. 容器的使用是比较灵活的，一个软件可以放置在不同的容器当中。


## RPM软件管理程序：rpm

RPM数据库文件所在的目录为：`/var/lib/rpm`，当软件安装完毕后，该软件相关的信息都会被写入该目录下的数据库文件当中。

安装软件涉及到的几个目录：
1. `/etc`：存放配置文件；
2. `/usr/bin`：存放可执行文件；
3. `/usr/lib`：存放程序使用的动态函数库；
4. `/usr/share/doc`：存放软件使用手册与帮助文档；
5. `/usr/share/man`：存放一些man手册页；


### RPM安装（`-i --install`）

重要说明：安装软件是root用户的工作，所以使用root的身份才能运行rpm命令。

基本语法：
1. 安装位于本地的软件：`rpm -ivh software.x86_64.rpm`
2. 安装位于网络的软件：`rpm -ivh http://website.com/path/software.x86_64.rpm`

参数说明：
1. `-i`：install，安装
2. `-v`：verbose，详细信息
3. `-h`：hash, 显示安装进度
4. `--nodeps`：在安装或更新软件时，不进行依赖检查
5. `--replacefiles`：覆盖已经安装的文件，这些文件可能是其他软件安装的
6. `--replacepkgs`：如果该软件已经安装过，则再重新安装一次
7. `--oldpackage`：如果已经安装过该软件，并且当前安装的软件版本比已经安装的软件版本旧的话，也允许继续安装当前的软件版本
8. `--force`：--replacefiles, --replacepkgs 和 --oldpackage 这三者的合体
9. `--test`：检查安装该软件是否存在依赖性问题或者冲突，不会实际安装软件
10. `--justdb`：更新软件在rpm数据库中的信息，不会更新文件系统，一般在rpm数据库损坏或者出现异常时使用
11. `--nosignature`：不进行数字证书检查
12. `--prefix`：将软件安装到指定目录
13. `--noscripts`：禁止在软件安装的时候自动执行某些系统命令


### RPM升级（`-U --upgrade` `-F --freshen`）

基本语法：
1. `rpm -Uvh software.x86_64.rpm`
2. `rpm -Fvh software.x86_64.rpm`

-U和-F的区别在于：如果软件没有安装，-U会进行安装；而-F不会安装。


### RPM查询（`-q --query`）

说明：RPM在查询的时候，实际上查询的是`/var/lib/rpm`目录下的数据库文件。

基本语法：
1. 查询所有已安装的软件：`rpm -qa`
1. 查询软件信息：`rpm -q[licdR] software`；这里指定软件名称就可以了，不需要加上版本等信息
2. 查询文件属于哪个软件：`rpm -qf file`
3. 查询未安装的.rpm包信息：`rpm -qp[licdR] software.rpm`

参数说明：
1. `-qa`：查询所有已安装的软件
2. `-q`：查询指定软件是否已安装
3. `-qi`：查询指定软件的详细信息
4. `-ql`：查询指定软件所有的文件和目录的详细路径
5. `-qc`：查询指定软件所有的配置文件
6. `-qd`：查询指定软件所有的帮助文件，`-d, --docfiles`
7. `-qR`：查询指定软件所有的依赖文件，`-R, --requires`
8. `-qf`：查询文件属于哪个软件
9. `-qp[licdR]`：在未安装的RPM包中查询信息，而不是在系统已安装的软件中查询，`-p, --package`


### RPM验证与数字证书

略。


### RPM卸载（`-e --erase`）

基本语法：
1. 卸载软件：`rpm -e software`


### RPM数据库重建（`--rebuilddb`）

基本语法：
1. 重建RPM数据库：`rpm --rebuilddb`


## SRPM的使用：rpmbuild

### 利用默认值安装SRPM文件

略。


### SRPM使用的路径与需要的软件

下载：[rp-pppoe-3.11-5.el7.src.rpm](https://pkgs.org/centos-7/centos-x86_64/rp-pppoe-3.11-5.el7.x86_64.rpm/download/)


```
[root@CentOS temp]# rpmbuild --rebuild rp-pppoe-3.11-5.el7.src.rpm
[root@CentOS rpmbuild]# pwd
/root/rpmbuild
[root@CentOS rpmbuild]# ls
BUILD  BUILDROOT  RPMS  SOURCES  SPECS  SRPMS
[root@CentOS x86_64]# pwd
/root/rpmbuild/RPMS/x86_64
[root@CentOS x86_64]# ll
total 284
-rw-r--r-- 1 root root 113688 Oct 27 00:03 rp-pppoe-3.11-5.el7.centos.x86_64.rpm
-rw-r--r-- 1 root root 174644 Oct 27 00:03 rp-pppoe-debuginfo-3.11-5.el7.centos.x86_64.rpm
```


### 设置文件的注意内容（`.spec`）

略。


### SRPM的编译命令（`-ba` / `-bb`）

略。


### 一个打包自己软件的范例

略。


## YUM在线升级机制

### 利用yum进行查询、安装、升级与删除功能

略。


### yum的设置文件

略。


### yum的软件组功能


### 全系统自动升级

增加一个定时任务即可，如：在凌晨3点进行软件的升级

```bash
# vim /etc/crontab
0 3 * * * root /usr/bin/yum -y update
```


## 管理的抉择：RPM还是Tarball

略。


## 重点回顾

略。


## 本章习题

略。


## 参考数据与扩展阅读

1. [http://www.study-area.org/tips/rpm.htm](http://www.study-area.org/tips/rpm.htm)

