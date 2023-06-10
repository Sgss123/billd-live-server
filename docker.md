## mysql

> https://hub.docker.com/_/mysql

### 拉镜像

```bash
docker pull mysql:8.0
```

### 复制配置文件到本地

先查看配置文件位置：

```bash
docker run --rm mysql:8.0 mysql --help | grep my.cnf
```

查看配置文件位置结果：

```bash
➜  billd-live-server git:(master) ✗ docker run --rm mysql:8.0 mysql --help | grep my.cnf
                      order of preference, my.cnf, $MYSQL_TCP_PORT,
/etc/my.cnf /etc/mysql/my.cnf /usr/etc/my.cnf ~/.my.cnf
➜  billd-live-server git:(master) ✗
```

意思是按照/etc/my.cnf /etc/mysql/my.cnf /usr/etc/my.cnf ~/.my.cnf 路径按优先排序。

```bash
➜  billd-live-server git:(master) ✗ docker run --rm mysql cat /etc/my.cnf
# For advice on how to change settings please see
# http://dev.mysql.com/doc/refman/8.0/en/server-configuration-defaults.html

[mysqld]
#
# Remove leading # and set to the amount of RAM for the most important data
省略...
➜  billd-live-server git:(master) ✗
```

可以得到镜像中 mysql 配置文件路径为：/etc/my.cnf

创建一个临时的容器，在它里面复制配置文件到本地：

> 注意，本地需要存在/Users/huangshuisheng/Desktop/docker/mysql/conf 这个目录

```bash
DOCKER_TMP=`docker run -d mysql:8.0` \
&& docker cp $DOCKER_TMP:/etc/my.cnf /Users/huangshuisheng/Desktop/docker/mysql/conf \
&& docker stop $DOCKER_TMP \
&& docker rm $DOCKER_TMP
```

### 启动容器

```bash
# 使用自定义 MySQL 配置文件
# billd-live-mysql是docker容器名，/Users/huangshuisheng/Desktop/docker/mysql是映射到本机的mysql，123456是密码

docker run -d \
-p 3306:3306 \
--name billd-live-mysql \
-e MYSQL_ROOT_PASSWORD=123456 \
-v /Users/huangshuisheng/Desktop/docker/mysql/data:/var/lib/mysql/ \
-v /Users/huangshuisheng/Desktop/docker/mysql/conf/my.cnf:/etc/my.cnf \
mysql:8.0
```