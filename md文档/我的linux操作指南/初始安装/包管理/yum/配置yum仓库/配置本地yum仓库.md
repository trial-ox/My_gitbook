```
我们本地有个iso的光盘，里面有很多软件包，简单来看看如何配置yum的客户端，然后通过挂载光盘
到文件系统上，通过本地文件系统安装管理软件包。
 
(1) 挂载光盘至某目录，例如/media/cdrom
mkdir -p /media/cdrom  #创建光盘挂载点
mount -r -t iso9660 /dev/cdrom /media/cdrom #只读挂载光盘文件到/dev/cdrom下(iso9660表示光盘的类型)
 
(2) 创建配置文件
最少可选模板，
[repository-id]
name=
baseurl=
gpgcheck=
enabled=
 
[root@node2 ~]# cd /etc/yum.repos.d/
[root@node2 yum.repos.d]# mkdir -p /media/cdrom
[root@node2 yum.repos.d]# mount -r -t iso9660 /dev/cdrom /media/cdrom/
[root@node2 yum.repos.d]# df -h
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda3        98G  1.5G   97G   2% /
devtmpfs        229M     0  229M   0% /dev
tmpfs           238M     0  238M   0% /dev/shm
tmpfs           238M  8.8M  230M   4% /run
tmpfs           238M     0  238M   0% /sys/fs/cgroup
/dev/sda1       397M   94M  303M  24% /boot
tmpfs            48M     0   48M   0% /run/user/0
tmpfs            48M     0   48M   0% /run/user/1000
tmpfs            48M     0   48M   0% /run/user/1007
/dev/sr0        4.1G  4.1G     0 100% /mnt
[root@node2 yum.repos.d]# ls -l /media/cdrom/
total 614
-rw-r--r-- 1 root root     16 Apr  1  2015 CentOS_BuildTag
drwxr-xr-x 3 root root   2048 Mar 28  2015 EFI
-rw-r--r-- 1 root root    215 Mar 28  2015 EULA
-rw-r--r-- 1 root root  18009 Mar 28  2015 GPL
drwxr-xr-x 3 root root   2048 Mar 28  2015 images
drwxr-xr-x 2 root root   2048 Mar 28  2015 isolinux
drwxr-xr-x 2 root root   2048 Mar 28  2015 LiveOS
drwxr-xr-x 2 root root 589824 Apr  1  2015 Packages
drwxr-xr-x 2 root root   4096 Apr  1  2015 repodata
-rw-r--r-- 1 root root   1690 Mar 28  2015 RPM-GPG-KEY-CentOS-7
-rw-r--r-- 1 root root   1690 Mar 28  2015 RPM-GPG-KEY-CentOS-Testing-7
-r--r--r-- 1 root root   2883 Apr  1  2015 TRANS.TBL
 
这是我配置的yum仓库客户端的配置文件
[root@node2 yum.repos.d]# cat local-base.repo
[CentOS7-localbase]
name=CentOS 7 local iso base
baseurl=file:///media/cdrom
gpgcheck=0
enabled=1
 
[root@node2 yum.repos.d]# yum repolist
Loaded plugins: fastestmirror
CentOS7-localbase                                                                                      | 3.6 kB  00:00:00     
(1/2): CentOS7-localbase/group_gz                                                                      | 154 kB  00:00:00     
(2/2): CentOS7-localbase/primary_db                                                                    | 2.7 MB  00:00:00     
Loading mirror speeds from cached hostfile
 * base: mirrors.163.com
 * extras: mirrors.shu.edu.cn
 * updates: mirrors.163.com
repo id                                                  repo name                                                      status
CentOS7-localbase                                        CentOS 7 local iso base                                        3,576
base/7/x86_64                                            CentOS-7 - Base                                                9,911
extras/7/x86_64                                          CentOS-7 - Extras                                                432
updates/7/x86_64                                         CentOS-7 - Updates                                             1,602
repolist: 15,521
上面可以看出，我们的配置的已经可以被查询出来了。
 
yum的repo配置文件中可用的变量：
    $releasever: 当前OS的发行版的主版本号；
    $arch: 平台；
    $basearch：基础平台；
    $YUM0-$YUM9
```

