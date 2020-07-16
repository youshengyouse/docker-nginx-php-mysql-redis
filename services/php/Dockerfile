# php版本在.env中定义
ARG PHP_VERSION
FROM ${PHP_VERSION}

ARG TZ
ARG PHP_EXTENSIONS
ARG CONTAINER_PACKAGE_URL


RUN sed -i "s/dl-cdn.alpinelinux.org/${CONTAINER_PACKAGE_URL}/g" /etc/apk/repositories


COPY ./extensions /tmp/extensions
WORKDIR /tmp/extensions
RUN chmod +x install.sh \
    && sh install.sh \
    && rm -rf /tmp/extensions


RUN apk --no-cache add tzdata \
    && cp "/usr/share/zoneinfo/$TZ" /etc/localtime \
    && echo "$TZ" > /etc/timezone

# 安装后可以使用ssh-keygen生成密钥对
RUN apk update && apk add --no-cache openssh-keygen

# 安装git - apt-get已被apk替代
RUN apk update && \
    apk upgrade && \
    apk add --no-cache bash git openssh


# Fix: https://github.com/docker-library/php/issues/240
RUN apk add gnu-libiconv --no-cache --repository http://${CONTAINER_PACKAGE_URL}/alpine/edge/community/ --allow-untrusted
ENV LD_PRELOAD /usr/lib/preloadable_libiconv.so php


# Install composer and change it's cache home
RUN curl -o /usr/bin/composer https://mirrors.aliyun.com/composer/composer.phar \
    && chmod +x /usr/bin/composer
ENV COMPOSER_HOME=/tmp/composer


# php image's www-data user uid & gid are 82, change them to 1000 (primary user)
RUN apk --no-cache add shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data

# gitee报UNPROTECTED PRIVATE KEY FILE错误
COPY ./root /root
USER root
RUN chmod 0600 /root/.ssh/id_rsa
RUN chmod -R 0777 /tmp

WORKDIR /