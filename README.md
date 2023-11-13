# DockerPHP

## 介绍

此镜像使用 `php:x.x.x-alpine` 作为基础镜像封装，操作系统为 `alpine`，且已安装 `nginx`。

- nginx 版本: 1.22.1
- php 版本: 8.1.17
- xdebug 版本: 3.1.4 (可选)
- composer 版本: 2.2.9

> ⚠️提示: 由于使用了新版特性，多个 `FROM`，仅支持 docker 17.05 后的版本

## 路径说明

- 工作目录: /app
- nginx.conf: /etc/nginx/nginx.conf
- php.ini: /usr/local/etc/php/php.ini
- php-fpm.conf: /usr/local/etc/php-fpm.d/zz-docker.conf

## 构建镜像

```bash
docker build --build-arg ENVIRONMENT=production -t edk24/docker-php:8.1.17 .
```

### 构建参数 (ARG)

| 参数名 | 默认值 | 取值范围 | 说明 |
| --- | --- | --- | --- |
| ENVIRONMENT | production | production, development | `development` 开发环境下会安装 `xdebug` 并配置 `9003` 为调试端口 |

## 快速运行

```bash
docker run --rm -it -v $(pwd):/app -w /app -p 3009:80 edk24/docker-php:8.1.17
```

访问: [http://localhost:3009](http://localhost:3009)