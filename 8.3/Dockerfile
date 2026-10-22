# PHP 8.3.36 为安全版本, 修复 CVE-2026-109618 / CVE-2026-109619 (Critical RCE)
FROM php:8.3.36-fpm-alpine3.24

# 环境: production | development
ARG ENVIRONMENT=production

ENV TIME_ZONE=Asia/Shanghai

# DockerEntrypoint
COPY docker/docker-entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh && mkdir -p /var/html/logs

# Copy php-fpm conf
COPY docker/conf/php/php-fpm.conf /usr/local/etc/php-fpm.d/zz-docker.conf

# php.ini
COPY docker/conf/php/php.ini /usr/local/etc/php/php.ini

# TUNA mirrors
# 注: 腾讯源 v3.24 索引滞后, 安装 musl-dev 时会把 musl 从 1.2.6-r3 降级到 r2, 故使用清华源
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.tuna.tsinghua.edu.cn/g' /etc/apk/repositories

# Install php extension
# 注: fileinfo / simplexml / xml / opcache / mbstring 已内置于官方镜像, 无需重复安装
RUN apk --no-cache add \
    libpng \
    libjpeg-turbo \
    libwebp \
    libzip \
    freetype \
    nginx \
    autoconf \
    g++ \
    make \
    git \
    libpng-dev \
    libjpeg-turbo-dev \
    libwebp-dev \
    zlib-dev \
    libzip-dev \
    freetype-dev \
    # GD extension
    && docker-php-ext-configure gd \
    --with-jpeg \
    --with-freetype \
    --with-webp \
    && docker-php-ext-install -j"$(nproc)" gd \
    # Other extension
    && docker-php-ext-install -j"$(nproc)" \
    zip \
    pcntl \
    bcmath \
    pdo_mysql \
    && rm -rf /var/cache/apk/*

# Install redis for php
RUN pecl channel-update pecl.php.net \
    && pecl install redis \
    && docker-php-ext-enable redis

# Install xdebug (xdebug 3.5 编译需要 linux-headers)
RUN if [ "$ENVIRONMENT" = "development" ]; then \
    apk --no-cache add linux-headers \
    && pecl install xdebug-3.5.3 \
    && docker-php-ext-enable xdebug \
    && echo -e "\n[XDebug]\nxdebug.mode = debug\nxdebug.start_with_request = yes\nxdebug.client_port = 9003\n" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini ; fi

# Install composer
RUN curl -o /usr/local/bin/composer https://mirrors.cloud.tencent.com/composer/composer.phar \
    && chmod +x /usr/local/bin/composer \
    && /usr/local/bin/composer config -g repo.packagist composer https://mirrors.cloud.tencent.com/composer/ \
    && mkdir -p /.composer

# Copy nginx config
COPY docker/conf/nginx/nginx.conf /etc/nginx/nginx.conf

WORKDIR /app

ENTRYPOINT [ "sh", "/entrypoint.sh" ]
