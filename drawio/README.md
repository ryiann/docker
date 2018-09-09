# drawio

### 简介

本镜像基于 `drawio` 官方`README.md`描述文件制作，调整如下

#### latest Tag

- 无修改，和官方Dockerfile保持一致

#### tomcat-8.0.53-jre8-alpine Tag

- 较小的体积
- 修改默认语言（简体中文）
- 下载因GFW网络原因导致无法访问到的静态文件
- 修改tomcat 8080端口默认项目为 draw

### 使用

#### latest Tag：

```
docker pull ryaning/drawio
```

#### tomcat-8.0.53-jre8-alpine Tag：

```
docker pull ryaning/drawio:tomcat-8.0.53-jre8-alpine
```
---

### 启动

命令说明：

- –name：表示容器名称，自定义名称
- -p：表示宿主机与容器的端口映射，此时将容器内部的 8080 端口映射为宿主机的 8080 端口，这样就向外界暴露了 8080 端口，可通过 docker 网桥来访问容器内部的 8080 端口了
- -v /etc/localtime:/etc/localtime:ro：将系统时区挂载容器内，保证容器时区和宿主机一致
- -v /home/drawio/logs:/usr/local/tomcat/logs ：将tomcat日志挂载到系统/home/drawio/logs目录下

#### latest Tag：

```
docker run --name drawio -p 8080:8080 -d ryaning/drawio:latest
```

#### tomcat-8.0.53-jre8-alpine Tag：

```
docker run --name drawio \
-p 8080:8080 \
-v /etc/localtime:/etc/localtime:ro \
-v /home/drawio/logs:/usr/local/tomcat/logs \
-d ryaning/drawio:tomcat-8.0.53-jre8-alpine
```

- 注：**初次使用先重启drawio镜像，目的是为了重新读取 server.xml 文件，使得 tomcat 默认项目为 draw**

```
docker restart drawio
```

### 访问

```
http://ip:8080
```
