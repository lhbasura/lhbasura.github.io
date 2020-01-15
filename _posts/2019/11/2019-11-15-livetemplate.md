---  
layout:     post
title:      idea上实用liveTemplate总结（持续更新）
subtitle:   livetemplate
date:       2019-11-15
author:     lhbasura
header-img: 
keywords_post:  "livetemplate"
catalog: true
tags:
    -   livetemplate
---  

## 打印入参日志

`LOG.info("收到 $class$.$method$ 调用请求，入参：$holder$", $params$);`

```groovy
className() //class
methodName() //method
groovyScript("_1.collect{it+'={}'}.join('，')",methodParameters()) //holder
groovyScript("_1.collect{it}.join(', ')",methodParameters()) //params
```
