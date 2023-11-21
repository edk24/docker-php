# DockerPHP

## 介绍

此镜像使用 `php:x.x.x-alpine` 作为基础镜像封装，操作系统为 `alpine`，且已安装 `nginx`。

- nginx
- php
- xdebug (dev版本携带, 端口9003)
- composer 2.2.9 (腾讯源)

## 路径说明

- 工作目录: /app
- nginx.conf: /etc/nginx/nginx.conf
- php.ini: /usr/local/etc/php/php.ini
- php-fpm.conf: /usr/local/etc/php-fpm.d/zz-docker.conf

## 直接使用已构建好的镜像

```bash
docker pull edk24/docker-php
```

## 自行构建镜像

[github仓库](https://github.com/edk24/docker-php)

```bash
# 构建镜像
$ docker build --build-arg ENVIRONMENT=production -t edk24/docker-php:8.1.17 .

# 使用 buildx 构建多平台镜像并推送
$ docker buildx build --build-arg ENVIRONMENT=production -t edk24/docker-php:8.1.17 --platform linux/amd64,linux/arm/v6,linux/arm/v7,linux/arm64/v8,linux/386 --push .
```

### 构建参数 (ARG)

| 参数名 | 默认值 | 取值范围 | 说明 |
| --- | --- | --- | --- |
| ENVIRONMENT | production | production, development | `development` 开发环境下会安装 `xdebug` 并配置 `9003` 为调试端口 |

## 快速运行

```bash
docker run --rm -it -v $(pwd):/app -w /app -p 3009:80 edk24/docker-php:8.1
```

访问: [http://localhost:3009](http://localhost:3009)

## 联系我

wechat: YmFzZTEwMjQ=
