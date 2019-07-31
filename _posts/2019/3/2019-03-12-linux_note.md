---  
layout:     post
title:      linux 笔记 
subtitle:   linux
date:       2019-03-12
author:     lhbasura 
header-img: 
keywords_post:  "linux"
catalog: true
tags:
    - linux
---  

## 查找大文件  

#### 查找超过800M大小的文件的文件名称
```
find . -type f -size +800M
```

#### 查找超过800M大小的文件的文件名称并详细显示文件属性或信息  
```
find . -type f -size +800M  -print0 | xargs -0 ls -l
```  

#### 查找超过800M大小文件，并显示查找出来文件的具体大小   
```
find . -type f -size +800M  -print0 | xargs -0 du -h
```

#### 查找超过800M大小文件，并对查找结果按照文件大小做排序
```
find . -type f -size +800M  -print0 | xargs -0 du -h | sort -nr
```
