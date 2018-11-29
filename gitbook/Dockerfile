# 基础镜像
FROM node:alpine
# 作者
MAINTAINER Ryan <me@ryana.cn>
# 构建镜像
RUN npm install --global gitbook-cli \
        && gitbook fetch\
        && npm cache clear --force \
        && rm -rf /tmp/*
# 工作目录
WORKDIR /srv/gitbook
# 挂载卷
VOLUME /srv/gitbook /srv/html
# 容器运行时监听的端口
EXPOSE 4000 35729
# 启动时运行
CMD /usr/local/bin/gitbook serve