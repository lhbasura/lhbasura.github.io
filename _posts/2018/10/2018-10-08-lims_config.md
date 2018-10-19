---
layout:     post
title:      VPN上部署的lims3.0基础服务
subtitle:   lims_config
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

## nginx  

```
docker run \
	--name nginx_lims3 \
	-d \
	-v /home/hongbo.liu/config/nginx/nginx.conf:/etc/nginx/nginx.conf \
        -v /home/hongbo.liu/config/nginx/fastcgi_params:/etc/nginx/fastcgi_params \
	-v /home/hongbo.liu/config/nginx/sites-enabled:/etc/nginx/sites-enabled \
	-v /home/hongbo.liu/config/nginx/sites-available:/etc/nginx/sites-available \
	-v /home/hongbo.liu/config/nginx/conf.d:/etc/nginx/conf.d \
	-v /home/hongbo.liu/log/nginx:/var/log/nginx \
	-v /home/hongbo.liu/project/lims2:/var/lib/lims2:rw \
	-p 88:80/tcp \
	--restart=always \
	--privileged \
	nginx:1.11-alpine  
```  

## lims3  

```
docker run \
    --name=lims3 \
    -d \
    -v /home/hongbo.liu/project/lims2:/var/lib/lims2 \
    -v /home/hongbo.liu/disk:/home/disk \
-v /home/hongbo.liu/log/php:/var/log/php7/\
    -p 66:9000/tcp \
    --restart=always \
    --privileged \
    docker.genee.in/genee/lims2:v1.1.1-d2018011101
```  
>注：这里用的还是之前lims2版本的docker镜像为了区分命名为lims3  

## mariadb  

```
docker run \
    --name=mariadb_lims3 \
    -d \
    -v /home/hongbo.liu/lib/mysql:/var/lib/mysql \
    -v /var/log/mysql:/var/log/mysql \
    -v /home/hongbo.liu/config/mariadb/my.cnf:/etc/mysql/my.cnf \
    -v /home/hongbo.liu/config/mariadb/conf.d/binlog.cnf:/etc/mysql/conf.d/binlog.cnf \
    -v /home/hongbo.liu/config/mariadb/conf.d/mysqld_safe_syslog.cnf:/etc/mysql/conf.d/mysqld_safe_syslog.cnf \
    -p 3305:3306 \
    --restart=always \
    docker.genee.in/genee/mariadb:v10.1.10-d2015122701
```
 >数据文件直接可用(位于vpn上的/home/hongbo.liu/lib，这里就不传github了)   
  
# redis 

```
docker run \
    --name redis_lims3 \
    -d \
    -v /home/hongbo.liu/config/redis/config/:/etc/redis \
    -v /home/hongbo.liu/config/redis/lib/:/var/lib/redis \
    -p  6380:6379 \
    --restart=always \
    docker.genee.in/genee/redis:v2.8.17-d2015080301
```

未完待续......