# 基础镜像
FROM ryaning/alpine-base:latest
# 作者
MAINTAINER  Ryan <chen110509@outlook.com>
# 定义构建参数
ARG LEANOTE_VERSION=2.6.1
# 创建镜像
RUN apk add --no-cache --update wget ca-certificates \
    && wget https://jaist.dl.sourceforge.net/project/leanote-bin/${LEANOTE_VERSION}/leanote-linux-amd64-v${LEANOTE_VERSION}.bin.tar.gz \
    && tar -zxvf leanote-linux-amd64-v${LEANOTE_VERSION}.bin.tar.gz -C / \
    && rm -rf /leanote/mongodb_backup \
    && rm leanote-linux-amd64-v${LEANOTE_VERSION}.bin.tar.gz \
    && chmod a+x /leanote/bin/run.sh && chmod a+x /leanote/bin/leanote-linux-amd64 \
    && sed -i '/chmod/d' /leanote/bin/run.sh \
    && apk del --purge wget
# 挂载卷
VOLUME /leanote/public/upload
# 容器运行时监听的端口
EXPOSE 9000
# 启动时运行leanote
CMD ["sh", "/leanote/bin/run.sh"]