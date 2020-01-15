---  
layout:     post
title:      mysql8 ：客户端连接caching-sha2-password问题
subtitle:   livetemplate
date:       2019-11-15
author:     
header-img: 
keywords_post:  "mysql8.0"
catalog: true
tags:
    -   mysql8.0
---  

## 在安装mysql8的时候如果选择了密码加密，之后用客户端连接比如navicate，会提示客户端连接caching-sha2-password,是由于客户端不支持这种插件，可以通过如下方式进行修改：
```
#修改加密规则  
ALTER USER 'root'@'localhost' IDENTIFIED BY 'password' PASSWORD EXPIRE NEVER; 
#更新密码（mysql_native_password模式）    
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '{NewPassword}';
```
原文[地址](https://www.cnblogs.com/xieshuang/p/9028362.html)