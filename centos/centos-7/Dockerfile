# 基础镜像
FROM centos:latest
# 作者
MAINTAINER  Ryan <chen110509@outlook.com>
# 构建镜像
RUN yum -y install openssh-server \
    && yum -y install openssh-clients \
	&& yum clean all \
	&& mkdir /var/run/sshd \
	&& ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key \
	&& ssh-keygen -t dsa -f /etc/ssh/ssh_host_dsa_key \
	&& /bin/echo 'root:123456'|chpasswd \
	&& /bin/sed -i 's/.*session.*required.*pam_loginuid.so.*/session optional pam_loginuid.so/g' /etc/pam.d/sshd \
	&& /bin/echo -e "LANG=\"en_US.UTF-8\"" > /etc/default/local	
# 容器运行时监听的端口
EXPOSE 22
# 启动时运行
CMD /usr/sbin/sshd -D