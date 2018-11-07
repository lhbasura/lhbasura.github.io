---  
layout:     post
title:      lims3.0对接gapper
subtitle:   lims3.0 to gapper
date:       2018-11-05
author:     lhbasura
header-img: img/lims_to_gapper.jpg
keywords_post:  "lims gapper"
catalog: true
tags:
    -   lims
    -   gapper
---  
## 总体设计  
原本lims系统中的权限设计如下：
* 将permission赋予role（n对1）
* 将role赋予用户（n对1）
* 用户具有被赋予的角色的所有权限  

对接gapper后权限体系将变成下面这样：
* 用户的基本信息在lims系统
* 用户的权限信息在gapper
* lims用户进行权限相关操作时请求gapper，gapper返回该用户具有的所有权限
  
具体流程如下：
![lims3_to_gapper](/img/lims3_to_gapper.png)

具体赋权方式如下：
![lims3_to_gapper](/img/lims3_permission_admin.png)


## lims与gapper之间的交互  

#### lims用户注册  
注册过程中用户输入账号密码及个人信息后在lims后台通过验证，
成功后将用户的后台将`user_id`、`client_secret`等发送到gapper，gapper返回`server_token`
需要接口（暂定）：  
request: 
```
{
    "client_id"    : "62747d17c19a7813",
    "client_secret": "562ab3f182e947a724a6160c1c7a334e",
    "users": [
        {  
            "id"    : "32",
            ......
        }
    ]
}
```

response:
````
{
    "server_token":""
    "status" : "success"
}
````

#### lims用户登录  
登录过程中用户输入账号密码后后台验证，验证通过后生成`user_token`,
后台将`user_token`、`user_id`、`client_secret`等发送到gapper，gapper返回`server_token`
需要接口（暂定）：  
request: 
```
{
    "client_id"    : "62747d17c19a7813",
    "client_secret": "562ab3f182e947a724a6160c1c7a334e",
    "users": [
        {  
            "token":"",
            "id"    : "32",
            ......
        }
    ]
}
```

response:
````
{
    "server_token":""
    "status" : "success"
}
````



#### 各模块运作  
当操作系统时，需要通过`user_token`在gapper上进行权限获取，
gapper返回该用户具有`permission`以及`server_token`,lims3需要对`server_token`进行比对，
从而确定`permission`的可靠性。
需要接口（暂定）：  
request: 
```
{
    "client_id"    : "62747d17c19a7813",
    "client_secret": "562ab3f182e947a724a6160c1c7a334e",
    "user": 
        {  
            "token":"",
            ......
        }
}
```

response:
````
{
    "server_token":""
    "permission":[
        ...
        ...
    ]
    "status" : "success"
}
````

#### 用户权限管理 
1. 授权用户请求gapper获取其所具有的所有`permission`  (见上)  
2. 授权用户把自己的`user_token`将被赋予的`permission`和被授权用户的`user_id`发送给gapper，gapper返回状态`status`
和server_token
需要接口（暂定）：  
request: 
```
{
    "client_id"    : "62747d17c19a7813",
    "client_secret": "562ab3f182e947a724a6160c1c7a334e",
    "useradmin": 
        {  
            "token":"",
            "user_id":"",
            permission:[
                ...
                ...
            ]
        }
}
```
response:
````
{
    "server_token":"",
    "status" : "success"
}
````

#### 值得注意的点
整个交互过程中应该采取双向认证的方式


##规模与时间

