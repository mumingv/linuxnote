# FAQ

## 多个用户如何使用同一个终端实现操作同步？


## 如何从头配置yum源？

### 查看当前使用的源

一般CentOS系统中会有一些已经安装好的源，如: 基础源CentOS-Base、epel源、remi源等。如下所示：

```
# cd /etc/yum.repos.d/
# ll
-rw-r--r-- 1 root root 2573 May 15  2015 CentOS-Base.repo
-rw-r--r-- 1 root root 1294 Jan 14 14:47 epel.repo
-rw-r--r-- 1 root root 1056 Jan 14 14:49 epel-testing.repo
-rw-r--r-- 1 root root 1177 Jan 14 14:49 remi-php70.repo
-rw-r--r-- 1 root root 1177 Jan 14 14:48 remi-php71.repo
-rw-r--r-- 1 root root 2340 Jan 14 14:48 remi.repo
-rw-r--r-- 1 root root  449 Jan 14 14:48 remi-safe.repo
```

源对应的pgpkey如下：

```
# cd /etc/pki/rpm-gpg/
# ll
-rw-r--r-- 1 root root 1662 Jul 24 05:37 RPM-GPG-KEY-EPEL-7
-rw-r--r-- 1 root root 1340 May  9  2010 RPM-GPG-KEY-remi
```

<font color="red">这里没有看到CentOS-Base对应的pgpkey，是因为在repo文件中指定了网络地址。如：
gpgkey=http://mirrors.aliyun.com/centos/RPM-GPG-KEY-CentOS-7
</font>


### 卸载当前使用的源

卸载epel源和remi源。由于remi源依赖于epel源，所以删除epel源即可同时删除remi源。

```
# yum remove epel-release
# yum clean all
```

卸载CentOS-Base源，直接删除源文件CentOS-Base.repo即可。


### 安装阿里云基础源CentOS-Base

参考：[阿里云指导帮助](http://mirrors.aliyun.com/help/centos)。

```
# wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
# yum makecache
```


### 安装epel源

安装epel源。

```
# yum install http://mirrors.aliyun.com/epel/epel-release-latest-7.noarch.rpm
```

修改repo配置(/etc/yum.repos.d/epel.repo)，将baseurl修改为阿里源。参考：[Github](https://github.com/mumingv/linux/blob/master/backup/repo/epel.repo)。

```
# yum makecache
```

注：将epel相关repo配置文件中的gpgcheck配置参数设置为0，可以避免gpgcheck失败导致的问题。


### 安装remi源

<font color="red">说明：remi源依赖于epel源。</font>

参考：[安装命令生成向导](https://rpms.remirepo.net/wizard/)。

```
# yum install http://rpms.remirepo.net/enterprise/remi-release-7.rpm
# yum makecache
```

注：将remi相关repo配置文件中的gpgcheck配置参数设置为0，可以避免gpgcheck失败导致的问题。


### 补充

1.查看目前激活的repo。

```
# yum repolist
Loaded plugins: langpacks
repo id                 repo name                                                         status
base/7/x86_64           CentOS-7 - Base - mirrors.aliyun.com                               9,363
epel/x86_64             Extra Packages for Enterprise Linux 7 - x86_64                    10,976
extras/7/x86_64         CentOS-7 - Extras - mirrors.aliyun.com                               199
remi-safe               Safe Remi's RPM repository for Enterprise Linux 7 - x86_64         1,985
updates/7/x86_64        CentOS-7 - Updates - mirrors.aliyun.com                              438
repolist: 22,961
```

2.gpgkey存放目录：

```
/etc/pki/rpm-gpg
```

3.将php56版本安装到/usr/bin目录，而不是/opt/remi/php56目录。

禁用remi-safe，启用remi-php56。

```
# yum-config-manager --disable remi-safe
# yum-config-manager --enable remi-php56
# yum install php
```


## 如何使用非root用户设置程序的开机启动？

以开机时自动使用work账号启动nginx和php-fpm程序为例。

1.准备执行脚本，包含启动程序的命令。

```
$ cat git/linux/script/startup.sh 
#!/bin/sh
/home/work/bdp/webserver/loadnginx.sh restart
/home/work/bdp/php/sbin/php-fpm restart
```

2.将使用work账号执行脚本的命令放入rc.local文件，并给该文件增加可执行权限。

```
$ cat /etc/rc.d/rc.local 
#!/bin/bash
#...
su work -c "sh /home/work/git/linux/script/startup.sh"
```
```
chmod +x /etc/rc.d/rc.local
```


## 如何查询CPU的核数？

```
$ grep processor /proc/cpuinfo | wc -l  
12
```


