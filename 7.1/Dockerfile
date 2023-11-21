FROM composer:2.2 as composer

FROM php:7.1.33-fpm-alpine3.10

# 环境: production | development
ARG ENVIRONMENT=production

ENV TIME_ZONE=Asia/Shanghai
ENV BUILD_DEPS='autoconf gcc g++ make bash'

# Install composer
COPY --from=composer /usr/bin/composer /usr/bin/composer
RUN /usr/bin/composer config -g repo.packagist composer https://mirrors.cloud.tencent.com/composer/

# DockerEntrypoint
COPY docker/docker-entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh && mkdir -p /var/html/logs

# Copy php-fpm conf
COPY docker/conf/php/php-fpm.conf /usr/local/etc/php-fpm.d/zz-docker.conf

# php.ini
COPY docker/conf/php/php.ini /usr/local/etc/php/php.ini

# Tencent mirrors
RUN sed -i 's/dl-cdn.alpinelinux.org/mirrors.cloud.tencent.com/g' /etc/apk/repositories

# Install php extension
RUN apk --no-cache add libpng autoconf g++ libxml2-dev make git libpng-dev libjpeg-turbo-dev libwebp-dev zlib-dev libxpm-dev libzip libzip-dev freetype freetype-dev gettext-dev nginx \
    # GD extension
    && docker-php-ext-configure gd \
    --with-jpeg-dir=/usr/include/ \
    --with-freetype-dir=/usr/include/ \
    --with-png-dir=/usr/include \
    --with-webp-dir=/usr/include/webp \
    && docker-php-ext-install -j$(nproc) gd \
    # Ohter extension
    && docker-php-ext-install \
    zip \
    pcntl \
    bcmath \
    pdo_mysql \ 
    fileinfo \
    simplexml \
    xml \
    opcache \
    && rm -rf /var/cache/apk/* \ 
    && docker-php-ext-enable gd zip pcntl bcmath pdo_mysql


# Install xdebug-2.9.0
RUN if [ "$ENVIRONMENT" = "development" ]; then \
    pecl install xdebug-2.9.0 \
    && docker-php-ext-enable xdebug \
    && echo -e "\n[XDebug]\nxdebug.remote_enable = 1\nxdebug.remote_autostart = 1\nxdebug.remote_port = 9003\n" >> /usr/local/etc/php/conf.d/docker-php-ext-xdebug.ini ; fi

# Copy nginx config
COPY docker/conf/nginx/nginx.conf /etc/nginx/nginx.conf

WORKDIR /app

ENTRYPOINT [ "sh", "/entrypoint.sh" ]



