FROM php:8.1.17-fpm-alpine

# 环境: production | development
ARG ENVIRONMENT=production

ENV TIME_ZONE=Asia/Shanghai
ENV BUILD_DEPS='autoconf gcc g++ make bash'

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
    --with-jpeg=/usr/include/ \
    --with-freetype=/usr/include/ \
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
    && rm -rf /var/cache/apk/*



# Install redis for php
RUN pecl channel-update pecl.php.net \
    && pecl install redis \
    && docker-php-ext-enable redis gd zip pcntl bcmath pdo_mysql

# Install xdebug-3.1.4
RUN if [ "$ENVIRONMENT" = "development" ]; then \
    pecl install xdebug-3.1.4 \
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



