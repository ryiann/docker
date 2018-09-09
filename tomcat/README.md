# tomcat

### 简介

本镜像基于官方`tomcat-alpine`镜像描述文件制作，调整如下

- 较小的体积
- 日志时间改为东八区
- 增加catalina.out日志

### 使用

tomcat7版本：

```
docker pull ryaning/tomcat:7.0.90-jre7-alpine-catalina
```

tomcat8版本：

```
docker pull ryaning/tomcat:8.0.53-jre8-alpine-catalina
```

### 启动

命令说明：

- –name：表示容器名称，自定义名称
- -p：表示宿主机与容器的端口映射，此时将容器内部的 8080 端口映射为宿主机的 8080 端口，这样就向外界暴露了 8080 端口，可通过 docker 网桥来访问容器内部的 8080 端口了
- -v /etc/localtime:/etc/localtime:ro：将系统时区挂载容器内，保证容器时区和宿主机一致

```
docker run --name tomcat \
-p 8080:8080 \
-v /etc/localtime:/etc/localtime:ro \
-d ryaning/tomcat:8.0.53-jre8-alpine-catalina
```
