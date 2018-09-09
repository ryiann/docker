# 基础镜像
FROM ryaning/tomcat:latest
# 作者
MAINTAINER  Ryan <chen110509@outlook.com>
# 拷贝文件
COPY server.xml /usr/local/tomcat/conf/
COPY draw.war /usr/local/tomcat/webapps/
# 容器运行时监听的端口
EXPOSE 8080
# 启动时运行
CMD ["catalina.sh", "run"]
