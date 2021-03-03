### apt命令解析

apt在apt-get上做的更改

| apt命令          | 取代的命令           | 命令的功能                     |
| ---------------- | -------------------- | ------------------------------ |
| apt install      | apt-get install      | 安装软件包                     |
| apt remove       | apt-get remove       | 移除软件包                     |
| apt purge        | apt-get purge        | 移除软件包及配置文件           |
| apt update       | apt-get update       | 刷新存储库索引                 |
| apt upgrade      | apt-get upgrade      | 升级所有可升级的软件包         |
| apt autoremove   | apt-get autoremove   | 自动删除不需要的包             |
| apt search       | apt-cache search     | 搜索应用程序                   |
| apt show         | apt-cache show       | 显示安装细节                   |
| apt full-upgrade | apt-get dist-upgrade | 在升级软件包时自动处理依赖关系 |

**新的apt命令**

| 新的apt命令      | 命令的功能                           |
| ---------------- | ------------------------------------ |
| apt list         | 列出包含条件的包（已安装，可升级等） |
| apt edit-sources | 编辑源列表                           |



1. **安装软件包**

   ```
   apt-get install PackageName                 // 普通安装
   apt-get install PackageName=VersionName     // 安装指定包的指定版本
   apt-get --reinstall install PackageName     // 重新安装
   
   apt-get build-dep PackageName               // 安装源码包所需要的编译环境
   apt-get -f install                          // 修复依赖关系
   
   apt-get source PackageName                  // 下载软件包的源码
   
   ```

2. **卸载 软件包**

   ```
   apt-get remove PackageName              // 删除软件包, 保留配置文件
   
   apt-get --purge remove PackageName      // 删除软件包, 同时删除配置文件
   apt-get purge PackageName               // 删除软件包, 同时删除配置文件
   
   apt-get autoremove PackageName          // 删除软件包, 同时删除为满足依赖
                                           // 而自动安装且不再使用的软件包
   
   apt-get --purge autoremove PackageName  // 删除软件包, 删除配置文件, 
                                           // 删除不再使用的依赖包
   
   apt-get clean && apt-get autoclean      // 清除 已下载的软件包 和 旧软件包
   
   ```

3. **更新软件包**

   ```
   apt-get update                  // 更新安装源（Source）
   apt-get upgrade                 // 更新已安装的软件包
   apt-get dist-upgrade            // 更新已安装的软件包（识别并处理依赖关系的改变）
   
   ```

4. **查询软件包**

   ```
   dpkg -l                             // 列出已安装的所有软件包
   
   apt-cache search PackageName        // 搜索软件包
   apt-cache show PackageName          // 获取软件包的相关信息, 如说明、大小、版本等
   
   apt-cache depends PackageName       // 查看该软件包需要哪些依赖包
   apt-cache rdepends PackageName      // 查看该软件包被哪些包依赖
   
   apt-get check                       // 检查是否有损坏的依赖
   
   ```

5. **apt-get --help**

   ```
   apt 1.0.1ubuntu2，用于 amd64 构架，编译于 Dec  8 2016 16:23:38
   用法： apt-get [选项] 命令
   　　　 apt-get [选项] install|remove 软件包1 [软件包2 ...]
   　　　 apt-get [选项] source 软件包1 [软件包2 ...]
   
   apt-get 是一个用于下载和安装软件包的简易命令行界面。
   最常用命令是 update 和 install。
   
   命令：
      update - 取回更新的软件包列表信息
      upgrade - 进行一次升级
      install - 安装新的软件包(注：软件包名称是 libc6 而非 libc6.deb)
      remove - 卸载软件包
      autoremove - 卸载所有自动安装且不再使用的软件包
      purge - 卸载并清除软件包的配置
      source - 下载源码包文件
      build-dep - 为源码包配置所需的编译依赖关系
      dist-upgrade - 发布版升级，见 apt-get(8)
      dselect-upgrade - 根据 dselect 的选择来进行升级
      clean - 删除所有已下载的包文件
      autoclean - 删除已下载的旧包文件
      check - 核对以确认系统的依赖关系的完整性
      changelog - 下载指定软件包，并显示其changelog
      download - 下载指定的二进制包到当前目录
   
   选项：
     -h  本帮助文档。
     -q  让输出可作为日志 - 不显示进度
     -qq 除了错误外，什么都不输出
     -d  仅仅下载 - 【不】安装或解开包文件
     -s  不作实际操作。只是依次模拟执行命令
     -y  对所有询问都回答是(Yes)，同时不作任何提示
     -f  当出现破损的依赖关系时，程序将尝试修正系统
     -m  当有包文件无法找到时，程序仍尝试继续执行
     -u  显示已升级的软件包列表
     -b  在下载完源码包后，编译生成相应的软件包
     -V  显示详尽的版本号
     -c=? 读取指定配置文件
     -o=? 设置任意指定的配置选项，例如 -o dir::cache=/tmp
   请查阅 apt-get(8)、sources.list(5) 和 apt.conf(5)的参考手册
   以获取更多信息和选项。
                          本 APT 具有超级牛力
   
   ```

   