---  
layout:     post
title:      vagrant+docker目录映射问题解决
subtitle:   lims3.0 solve
date:       2018-10-23
author:     lhbasura
header-img: img/lims.jpg
keywords_post:  "vagrant,docker"
catalog: true
tags:
    - vagrant
    - docker  
---  
# 说明  
我做开发时用到vagrant+docker的形式，我们需要将宿主机中的项目映射到vagrant再在vagrant中将其
挂载到docker，这时会出现一些问题，以下是我遇到的问题和解决方案

# 1.挂载到docker中的目录为空  
这是因为在宿主机还未将项目映射到vagrant中vagrant就将目录挂载到了docker中的缘故，我们可以通过`sudo systemctl restart docker` 命令重启docker解决如果不想每次手动重启可以再Vagrantfile中写入重启脚本
```
 config.vm.provision "shell", run:"always", inline: <<-SHELL
    sudo systemctl restart docker
 SHELL
```
# 2.项目文件加载速度慢  
这个问题是多半由于vagrant需要对项目文件与主机进行同步造成的，这里我们可以启用`NFS`来解决,我们只需要在Vagrantfile中
挂载目录的那一行末尾加上`type:'nfs'`就像这样

![Vagrantfile](/img/vagrantfile.png)

# 3.关于MacOS更新成Mojave版本后NFS无法启用问题
>MacOS更新成Mojave后原本nfs需要用到的`/etc`下的`exports`文件没有了，不管是
vagrant自动创建还是手动创建你会发现没有权限（即使是root）
这是MacOS的系统保护机制造成的，你需要给某个程序对磁盘的完全访问权限  

1. 打开Mac OS X的system settings> Security & Privacy> privacy选项卡

2. 选择Full Disk Access，然后单击加号图标。

3. 在列表中添加终端（在我的案例中为iTerm）

4. 重启iTerm

5. 在iterm中手动创建`/etc/exports`文件：   
```
sudo touch /etc/exports
```  
执行完应该可以看到`/etc`下创建的exports文件,这时候就可以按一般步骤启动vagrant了