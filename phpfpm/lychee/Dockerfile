# 基础镜像
FROM php:7.3-fpm-alpine3.9
# 作者
MAINTAINER  ryan <me@ryana.cn>
# 构建镜像
RUN docker-php-ext-install mysqli \
	&& docker-php-ext-install pdo_mysql\
	&& docker-php-ext-install exif
