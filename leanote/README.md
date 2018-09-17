# leanote

[leanote wiki][3]

文件地址：

[leanote-linux-amd64-v2.6.1.bin.tar.gz][1]   
[app.conf][2]

---

## 1、下载所需镜像

```
# 下载leanote镜像
docker pull ryaning/leanote
# 下载mongo镜像
docker pull mongo:4.0.0
```

## 2、初始化数据库

### 2.1、启动mongo数据库

命令说明：

- –name：自定义别名
- -v /etc/localtime:/etc/localtime:ro：将系统时区挂载容器内，保证容器时区和宿主机一致
- -v /home/leanote/mongo/db:/data/db：将主机中 /home/leanote/mongo/db 挂载到容器的 /data/db，作为mongo数据存储目录

```
docker run --name leanote-mongo \
-v /etc/localtime:/etc/localtime:ro \
-v /home/leanote/mongo/db:/data/db \
-d mongo:4.0.0
```

**注：** 这里为了数据安全考虑，没有映射mongo端口，可通过（`--link`）方式指定容器来使用


### 2.2、下载数据初始化文件

leanote数据库初始化文件默认位于`leanote-linux-amd64-v2.x.x.bin.tar.gz`的`/leanote/mongodb_backup/leanote_install_data`下，博主已经将该文件提取出来了，可以直接使用，不放心的话也可以从官方github上下载

```
wget http://soft.ryana.cn/%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F/Leanote/leanote_install_data.tar.gz
```

将数据库初始化文件拷贝到mongo容器内

```
docker cp leanote_install_data.tar.gz leanote-mongo:/home/
```

```
[root@server home]# docker ps
CONTAINER ID      IMAGE           COMMAND                  CREATED             STATUS              PORTS          NAMES
59cd627f4e96      mongo:4.0.0     "docker-entrypoint.s…"   14 seconds ago      Up 13 seconds       27017/tcp      leanote-mongo
[root@server home]# docker cp leanote_install_data.tar.gz leanote-mongo:/home/
[root@server home]#
```

### 2.3、进入容器初始化数据

```
docker exec -it leanote-mongo bash
```

进入容器后，解压并导入数据库文件

```
tar -zxvf /home/leanote_install_data.tar.gz -C /home/
mongorestore -h localhost -d leanote --dir /home/leanote_install_data
```

导入完成后删除初始数据文件

```
rm -rf /home/leanote_install_data*
```

### 2.4、重启mongo容器

退出容器，重启mongo

```
docker restart leanote-mongo
```

## 3、启动leanote

命令说明：

- –name：自定义别名
- -p 9000:9000 :将容器的 9000 端口映射到主机的 9000 端口
- -v /home/leanote/app.conf:/leanote/conf/app.conf：将主机中 /home/leanote/app.conf 挂载到容器的  /leanote/conf/app.conf，替换leanote配置
- -v /home/leanote/data:/leanote/public/upload：将主机中 /home/leanote/data 挂载到容器的  /leanote/public/upload，作为leanote数据目录
- --link leanote-mongo:mongohost：告诉当前容器需要使用`mongo`(leanote-mongo)容器，并重命名为mongohost
- -d：以守护进程方式运行

```
docker run --name leanote \
-p 9000:9000 \
-v /etc/localtime:/etc/localtime:ro \
-v /home/leanote/app.conf:/leanote/conf/app.conf \
-v /home/leanote/data:/leanote/public/upload \
--link leanote-mongo:mongohost \
-d ryaning/leanote
```

### app.conf

博主修改了`db.host=mongohost`和上方`--link leanote-mongo:mongohost`对应，当然为了安全考虑，还可以给mongo数据库添加访问账户密码（可参考文章：[docker安装mongo及开启用户认证][4]）

```
db.host=mongohost
db.port=27017
db.dbname=leanote # required
db.username= # if not exists, please leave blank
db.password= # if not exists, please leave blank
```

查看启动日志

```
docker logs leanote
```

---
[1]:https://jaist.dl.sourceforge.net/project/leanote-bin/${LEANOTE_VERSION}/leanote-linux-amd64-v${LEANOTE_VERSION}.bin.tar.gz
[2]:https://github.com/YoriChen/docker/blob/master/leanote/app.conf
[3]:https://github.com/leanote/leanote/wiki
[4]:http://blog.ryana.cn/#Docker%20%E5%AE%89%E8%A3%85%20Mongo%20%E5%8F%8A%E5%BC%80%E5%90%AF%E7%94%A8%E6%88%B7%E8%AE%A4%E8%AF%81