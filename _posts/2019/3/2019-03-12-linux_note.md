---  
layout:     post
title:      linux 空间清理 
subtitle:   linux
date:       2019-03-12
author:     lhbasura 
header-img: 
keywords_post:  "linux"
catalog: true
tags:
    - linux
---  

## 1.查看空间占用情况
命令：
```bash
  df -h
```
参数说明：

-a：列出所有的文件系统，包括系统特有的/proc等文件系统

-k：以KB的容量显示各文件系统

-m：以MB的容量显示各文件系统

-h：以人们较易阅读的GB,MB,KB等格式自行显示

-H：以M=1000K替代M=1024K的进位方式

-T：连同该分区的文件系统名称（例如ext3）也列出

-i：不用硬盘容量，而以inode的数量来显示

## 2.显示每个目录的大小
命令：
```bash
du -sh /*
```

du参数：

-a : 列出所有的文件与目录容量，因为默认仅统计目录下面的文件量而已；

-h : 以人们较易读的容量格式（G/M）显示；

-s : 列出总量，而不列出每个个别的目录占用了容量；

-S : 不包括子目录下的总计，与-s有点差别；

-k : 以KB列出容量显示；

-m : 以MB列出容量显示。

du -h --max-depth=1 寻找当前目录，哪个文件夹占用空间最大

## 3.文件排序
输入命令：
```bash
ls –lhS #将文件以从大到小顺序展现
```