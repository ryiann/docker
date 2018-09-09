# 基础镜像
FROM alpine:latest
# 作者
MAINTAINER  Ryan <chen110509@outlook.com>
# 替换系统时区
COPY localtime /etc/localtime
# 构建镜像
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.aliyun.com/g' /etc/apk/repositories
# 启动时运行
CMD ["/bin/bash"]