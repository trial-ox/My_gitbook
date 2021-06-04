```
语法结构：
yum [options] [command] [package ...]
 
options表示选项，command表示使用的命令，package表示软件包的名字；
 
command is one of:
 * install package1 [package2] [...]
 * update [package1] [package2] [...]
 * update-to [package1] [package2] [...]
 * update-minimal [package1] [package2] [...]
 * check-update
 * upgrade [package1] [package2] [...]
 * upgrade-to [package1] [package2] [...]
 * distribution-synchronization [package1] [package2] [...]
 * remove | erase package1 [package2] [...]
 * autoremove [package1] [...]
 * list [...]
 * info [...]
 * provides | whatprovides feature1 [feature2] [...]
 * clean [ packages | metadata | expire-cache | rpmdb | plugins | all ]
 * makecache [fast]
 * groups [...]
 * search string1 [string2] [...]
 * shell [filename]
 * resolvedep dep1 [dep2] [...]
    (maintained for legacy reasons only - use repoquery or yum provides)
 * localinstall rpmfile1 [rpmfile2] [...]
    (maintained for legacy reasons only - use install)
 * localupdate rpmfile1 [rpmfile2] [...]
    (maintained for legacy reasons only - use update)
 * reinstall package1 [package2] [...]
 * downgrade package1 [package2] [...]
 * deplist package1 [package2] [...]
 * repolist [all|enabled|disabled]
 * repoinfo [all|enabled|disabled]
 * repository-packages <enabled-repoid> <install|remove|remove-or-reinstall|remove-or-distribution-synchronization>
[package2] [...]
 * version [ all | installed | available | group-* | nogroups* | grouplist | groupinfo ]
 * history [info|list|packages-list|packages-info|summary|addon-info|redo|undo|rollback|new|sync|stats]
 * load-transaction [txfile]
 * updateinfo [summary | list | info | remove-pkgs-ts | exclude-updates | exclude-all | check-running-kernel]
 * fssnapshot [summary | list | have-space | create | delete]
 * fs [filters | refilter | refilter-cleanup | du]
 * check
 * help [command]
 
options：
1、repolist：显示仓库列表；
repolist [all|enabled|disabled]
如果指定enabled：表示只显示启用的仓库列表；
如果指定disabled：表示只显示关闭的仓库列表；
如果指定all：表示显示启用和关闭的仓库列表；
默认的repolist的参数是enabled。
repolist [all|enabled|disabled] 后边还可以通过仓库的id或者名字来查找，并且支持bash通配符机制；
repolist [all|enabled|disabled] "Repo-id"
repolist [all|enabled|disabled] "Repo-name"
 
有时候需要配置-v选项一起，查看详细信息；
 
2、repoinfo：查看仓库详细信息
repoinfo [all|enabled|disabled]
repoinfo命令和repolist [all|enabled|disabled] -v等价；
 
3、list：显示程序包
yum list [all | glob_exp1] [glob_exp2] [...]
列出所有可用和已经安装的软件包；
yum list available [glob_exp1] [...]
列出yum仓库所有可用的的安装包；
yum list updates [glob_exp1] [...]
列出yum仓库所有可用的更新包；
yum list installed [glob_exp1] [...]
列出所有已经安装的软件包；
yum list extras [glob_exp1] [...]
列出所有系统已经安装的软件包却不能被yum仓库的配置文件所列出(通过rpm安装的或者之前yum仓库安装
后来yum仓库被移除了等)软件包列表；
yum list distro-extras [glob_exp1] [...]
列出所有已经安装的软件包却不能根据yum配置文件列出的yum仓库的名字所匹配(yum仓库已经被移除了)软件包
列表；
yum list obsoletes [glob_exp1] [...]
列出系统已经安装却被rpm仓库的配置文件列表指向yum仓库人为软件包已经过时了的软件包列表；
yum list recent
   List  packages recently added into the repositories. This is often not helpful, but what you may really want
   to use is "yum list-updateinfo new" from the security yum plugin.
列出最近加入yum仓库的软件包列表。
 
4、install：安装
install package1 [package2] [...]
 
5、reinstall：重新安装
reinstall package1 [package2] [...]
 
6、update：升级更新
update [package1] [package2] [...]
不指定软件包名或者指定参考没有匹配(可以使用bash通配机制)软件包，会更新所有软件包；
 
7、downgrade：降级
downgrade package1 [package2] [...]
 
8、check-update：检查是否有可用的升级
返回状态值为100表示有可用的软件包升级(正常会返回可用的软件包更新列表)；
返回状态值为0表示无可用的软件包升级；
返回状态值为1表示运行报错；
 
9、remove or erase：卸载软件包
remove | erase package1 [package2] [...]
yum默认有保护yum自身的机制，不能对yum本身进行卸载；
 
10、info：查看程序包信息
列出描述和摘要信息，可选的指定选项和list一样，下面只理出，不再次说明含义。
yum info [all | glob_exp1] [glob_exp2] [...]
yum info available [glob_exp1] [...]
yum info updates [glob_exp1] [...]
yum info installed [glob_exp1] [...]
yum info extras [glob_exp1] [...]
yum info distro-extras [glob_exp1] [...]
yum info obsoletes [glob_exp1] [...]
yum info recent
用的最多的就是：
yum info package_name类似于rpm -qi package_name或者rpm -qpi package_name.xxx.rpm
 
11、search：搜索程序包
search [all] string1 [string2] [...]
以指定的关键字搜索程序包名及summary信息；
默认是根据报名和摘要信息进行搜索，如果检索失败，会去详细描述信息和url中去检索；
可以显式指明all来增大检索的范围，搜索的结果会有一定的排序；
 
12、provides 或 whatprovides：查看指定的特性是由哪个程序包提供
provides | whatprovides feature1 [feature2] [...]
 
13、clean：清理本地缓存
yum clean expire-cache  清理过期缓存；
yum clean packages   清理包；
yum clean headers  清理头文件；
yum clean metadata 清理元数据；
yum clean dbcache 清理sqlite数据库缓存；
yum clean rpmdb 清理本地的rpmdb缓存；
yum clean plugins 清理插件；
yum clean all 清理所有的
 
14、makecache：构建本地缓存
makecache [fast]
不加fast，会对所有启用的仓库构建本地缓存数据；
加fast，速度更快，不过只会对过期的缓存进行重新缓存，已经缓存且没过期的不会；
 
15、deplist：查看指定的软件包所依赖的能力(capabilities)
deplist package1 [package2] [...]
 
16、history：查看yum事务历史
history [info|list|packages-list|packages-info|summary|addon-info|redo|undo|rollback|new|sync|stats]
不常用，这里不进行讲解每个选项的含义。
 
17、安装及升级本地程序包：
* localinstall rpmfile1 [rpmfile2] [...]
   (maintained for legacy reasons only - use install)
* localupdate rpmfile1 [rpmfile2] [...]
   (maintained for legacy reasons only - use update)
这是一个早期的版本的选项，就是如果你本地有一个rpm文件，你可以直接通过yum 指定这个rpm文件(接的全名)
来安装(会解决依赖管理，去启用的仓库中寻找依赖的软件包)。
CentOS 6.x和CentOS 7.x直接使用：
yum install package_name.xxx.rpm
yum update package_name.xxx.rpm
 
18、包组管理相关：
* groupinstall group1 [group2] [...]   #安装开发包组
* groupupdate group1 [group2] [...]  #更新开发包组
* grouplist [hidden] [groupwildcard] [...] #列出开发包组的信息
* groupremove group1 [group2] [...] #卸载开发包组
* groupinfo group1 [...]  #查看开发包组的摘要信息(包含软件包列表)
 
yum的命令行选项：
--nogpgcheck：禁止进行gpg check；
-y: 自动回答为“yes”；
-q：静默模式；
--disablerepo=repoidglob：临时禁用此处指定的repo；
--enablerepo=repoidglob：临时启用此处指定的repo；
--noplugins：禁用所有插件；
```

