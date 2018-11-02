---
layout:     post
title:      VPN上部署的lims3.0基础服务
subtitle:   lims config
date:       2018-10-19
author:     lhbasura
header-img: img/banner_lims.jpg
keywords_post:  "lims,部署"
catalog: true
tags:
    - lims  
---
>以下为演示站上的配置  
演示站点上部署的lims3环境配置文件都可在[GitHub](https://github.com/lhbasura/lims2-deploy-doc)中获取

## 基础功能

#### nginx  

```
docker run \
	--name nginx_lims3 \
	-d \
	-v ~/config/nginx/nginx.conf:/etc/nginx/nginx.conf \
        -v ~/config/nginx/fastcgi_params:/etc/nginx/fastcgi_params \
	-v ~/config/nginx/sites-enabled:/etc/nginx/sites-enabled \
	-v ~/config/nginx/sites-available:/etc/nginx/sites-available \
	-v ~/config/nginx/conf.d:/etc/nginx/conf.d \
	-v ~/log/nginx:/var/log/nginx \
	-v ~/project/lims2:/var/lib/lims2:rw \
	-p 88:80/tcp \
	--restart=always \
	--privileged \
	nginx:1.11-alpine  
```  


#### mariadb  

```
docker run \
    --name=mariadb_lims3 \
    -d \
    -v ~/lib/mysql:/var/lib/mysql \
    -v /var/log/mysql:/var/log/mysql \
    -v ~/config/mariadb/my.cnf:/etc/mysql/my.cnf \
    -v ~/config/mariadb/conf.d/binlog.cnf:/etc/mysql/conf.d/binlog.cnf \
    -v ~/config/mariadb/conf.d/mysqld_safe_syslog.cnf:/etc/mysql/conf.d/mysqld_safe_syslog.cnf \
    -p 3305:3306 \
    --restart=always \
    docker.genee.in/genee/mariadb:v10.1.10-d2015122701
```
 >数据文件直接可用(位于vpn上我的家目录下的lib，这里就不传github了)   
 注：由于之前mariadb的3306端口已经被占用，所以这里改为3305需要之后在项目中`application/config/database.php`中进行修改

  
#### redis 

```
docker run \
    --name redis_lims3 \
    -d \
    -v ~/config/redis/config/:/etc/redis \
    -v ~/config/redis/lib/:/var/lib/redis \
    -p  6380:6379 \
    --restart=always \
    docker.genee.in/genee/redis:v2.8.17-d2015080301
```
>注：由于之前redis的6379端口已经被占用，所以这里改为6380需要之后在项目中`public/globals.php`中进行修改

#### lims3  

```
docker run \
    --name=lims3 \
    -d \
    -v ~/project/lims2:/var/lib/lims2 \
    -v ~/disk:/home/disk \
-v ~/log/php:/var/log/php7/\
    -p 66:9000/tcp \
    --restart=always \
    --privileged \
    docker.genee.in/genee/lims2:v1.1.1-d2018011101
```  
>注：这里用的还是之前lims2版本的docker镜像为了区分命名为lims3  

到这一步首先需要将工程下public/globals.example和application/config/database.expample进行拷贝
```
cd ~/project/lims2
cp public/globals.example public/globals.php
cp  application/config/database.example application/config/database.php
```
然后修改两个文件的对应`redis`和`mariadb`的端口为之前所述  
* redis修改
![redis](/img/redis_port.png)

* mariadb修改
![mariadb](/img/mariadb_port.png)

除此之外，还要将lims3容器中`php.ini`中的redis地址进行修改
此时应该可以进入系统，基础功能环境搭建完毕   


## 预约功能
预约需要开启两个服务，一个是reserv-server一个是beanstalkd  

#### reserv-server

```
docker run \
    --name=reserv-server_lims3 \
    -d \
    -v ~/config/reserv-server/config:/usr/src/app/config \
    -v ~/config/reserv-server/logs:/usr/src/app/logs \
    -p 172.17.42.1:9899:9898 \
    -p 172.17.42.1:9589:9509 \
    --restart=always \
    docker.genee.in/genee/reserv-server:v0.5.0-d2018011101
```

#### beanstalkd

```
docker run \
    --name beanstalkd_lims3 \
    -d \
    -v ~/config/beanstalkd/data:/data \
    --restart=always \
    -p 172.17.42.1:11388:11300 \
    docker.genee.in/genee/beanstalkd:v1.10.0-d2017112401
```
之后记得在`globals.php`中修改RESERV_POR和BEANSTALKD_PORT

未完待续......