# 基础镜像
FROM php:7.3-fpm-alpine3.9
# 作者
MAINTAINER  ryan <me@ryana.cn>
# 构建镜像
RUN apk --update libpng libpng-devel \
	&& docker-php-ext-install mysqli pdo_mysql exif gd
