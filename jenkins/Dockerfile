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
