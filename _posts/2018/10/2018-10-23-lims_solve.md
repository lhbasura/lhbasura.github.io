---  
layout:     post
title:      vagrant下lims3.0性能问题解决
subtitle:   lims3.0 solve
date:       2018-10-23
author:     lhbasura
header-img: img/lims.jpg
keywords_post:  "lims3.0,solve"
catalog: true
tags:
    - lims3.0
    - solve  
---  
# 说明  
如果是有vagrant+docker配置的lims会存在一些问题，这里给出存在问题的解决方案

# 1.页面加载速度慢  
这个问题是多半由于vagrant需要对项目文件与主机进行同步造成的，这里我们可以启用`NFS`来解决,我们只需要在Vagrantfile中
挂载目录的那一行末尾加上`type:'nfs'`就像这样

![Vagrantfile](/img/vagrantfile.png)

# 2.关于MacOS更新成Mojave版本后NFS无法启用问题
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