---  
layout:     post
title:      SpringSecurity的天坑解决 
subtitle:   SpringSecurity,thymeleaf
date:       2019-02-12
author:     lhbasura 
header-img: 
keywords_post:  "SpringSecurity,thymeleaf"
catalog: true
tags:
    - yarn
    - node  
---  
>原文 [链接](https://www.jianshu.com/p/f4dbe17bda2e)   
 
#### 安装node
```
curl --silent --location https://rpm.nodesource.com/setup_8.x | sudo bash -
sudo yum -y install nodejs`
```

#### 安装yarn
```
curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
sudo yum install yarn
```
    <input type="hidden"
                       th:name="${_csrf.parameterName}"
                       th:value="${_csrf.token}"/>