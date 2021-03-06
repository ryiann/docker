# jenkins

## 简介

Jenkins 获取 Tag 源码，编译、打包、构建镜像时，需要用到 Docker 命令，Jenkins 容器本身没有安装 Docker ，如何在 Docker 容器内使用 docker 命令？ 为了让容器内可以构建镜像，应该使用 Docker Remote API 的客户端来直接调用宿主的 Docker Engine，也就是所谓的 Docker In Docker (DIND)。

## 为 Jenkins 添加 Docker 命令行

本镜像以官方 `jenkins/jenkins:lts-alpine` 镜像为例，使用 Dockerfile 添加 docker 命令行可执行文件，并调整权限。

```
# 基础镜像
FROM jenkins/jenkins:lts-alpine
# 作者
MAINTAINER  ryan <me@ryana.cn>
# 安装 Docker CLI
USER root
RUN curl -O https://get.docker.com/builds/Linux/x86_64/docker-latest.tgz \
    && tar zxvf docker-latest.tgz \
    && cp docker/docker /usr/local/bin/ \
    && rm -rf docker docker-latest.tgz
# 将 `jenkins` 用户的组 ID 改为宿主 `docker` 组的组ID，从而具有执行 `docker` 命令的权限。
ARG DOCKER_GID=994
USER jenkins:${DOCKER_GID}
```

在上面这个 Dockerfile 例子中，我们下载了静态编译的 docker 可执行文件，并提取命令行安装到系统目录下。然后调整了 jenkins 用户的组 ID，调整为宿主 docker 组 ID，从而使其具有执行 docker 命令的权限。

组 ID 使用了 `DOCKER_GID` 参数来定义，以方便进一步定制，**ARG DOCKER_GID=994 只是个例子，您服务器上的 DOCKER_GID 可能不是 994**，可通过下面两种方式改变（二选一）：

* 命令 `cat /etc/group|grep docker` 查看 `DOCKER_GID`， 构建镜像时通过 `--build-arg` 来改变 `DOCKER_GID` 的默认值。
* 运行时通过 `--user jenkins:994` 来改变运行用户的身份（**墙裂推荐**）。

```
# 如果需要构建时调整 docker 组 ID，可以使用 --build-arg 来覆盖参数默认值
docker build -t jenkins --build-arg DOCKER_GID=994 .
# 在启动容器的时候，将宿主的 `/var/run/docker.sock` 文件挂载到容器内的同样位置，从而让容器内可以通过 unix socket 调用宿主的 Docker 引擎
docker run --name jenkins \
-p 8080:8080 \
-v /var/run/docker.sock:/var/run/docker.sock \
-d ryaning/jenkins
```

## 下载

```
docker pull ryaning/jenkins
```

## 启动

```
# 查看 docker 组的 gid
cat /etc/group|grep docker
# 例
[root@test ~]# cat /etc/group|grep docker
docker:x:994:
[root@test ~]# 
# 替换 docker 组的 gid，并运行
docker run --name jenkins \
-p 8080:8080 \
-p 50000:50000 \
-e TZ="Asia/Shanghai" \
-v /etc/localtime:/etc/localtime:ro \
-v /home/jenkins:/var/jenkins_home \
-v /var/run/docker.sock:/var/run/docker.sock \
--user jenkins:994 \
-d ryaning/jenkins
```

---

**注意：**在运行 jenkins 时，挂在文件夹的归属用户 id 必须是 1000 ，否则会抛出无操作权限异常。异常如下：

```
touch: cannot touch '/var/jenkins_home/copy_reference_file.log': Permission denied
Can not write to /var/jenkins_home/copy_reference_file.log. Wrong volume permissions?
```

为什么挂载文件夹的归属用户 Id 必须是 1000？在 Jenkins 的 Dockerfile 中可以看到说明要确保使用相同的 uid。

```
ARG user=jenkins
ARG group=jenkins
ARG uid=1000
ARG gid=1000
ARG http_port=8080
ARG agent_port=50000

ENV JENKINS_HOME /var/jenkins_home
ENV JENKINS_SLAVE_AGENT_PORT ${agent_port}

# Jenkins is run with user `jenkins`, uid = 1000
# If you bind mount a volume from the host or a data container, 
# ensure you use the same uid
RUN addgroup -g ${gid} ${group} \
    && adduser -h "$JENKINS_HOME" -u ${uid} -G ${group} -s /bin/bash -D ${user}
```

```
# 查看文件夹的归属者
ls -nd /home/jenkins/
# 修改文件夹的归属者和组
chown -R 1000:1000 /home/jenkins/
```
