# DockerPHP

## 介绍

此镜像使用 `php:x.x.x-alpine` 作为基础镜像封装，操作系统为 `alpine`，且已安装 `nginx`。

- nginx
- php
- xdebug (dev版本携带, 端口9003)
- composer (腾讯源)

## 版本说明

| PHP版本 | 基础镜像 | 说明 |
| --- | --- | --- |
| 8.3 | `php:8.3.36-fpm-alpine3.24` | 安全版本，修复 `CVE-2026-109618`、`CVE-2026-109619` |
| 8.1 | `php:8.1.25-fpm-alpine3.18` | |
| 7.4 | `php:7.4-fpm-alpine3.16` | |
| 7.3 | `php:7.3.33-fpm-alpine3.14` | |
| 7.1 | `php:7.1.33-fpm-alpine3.10` | |

> PHP 8.3.36 修复了 2 个严重 (Critical) 远程代码执行漏洞:
>
> - `CVE-2026-109618` RCE via OOM corruption ([GHSA-w8hw-w9fx-j79w](https://github.com/php/php-src/security/advisories/GHSA-w8hw-w9fx-j79w))
> - `CVE-2026-109619` RCE via stale global state ([GHSA-6wmf-4rv5-p3rp](https://github.com/php/php-src/security/advisories/GHSA-6wmf-4rv5-p3rp))
>
> 影响版本: `PHP < 8.2.35`、`< 8.3.36`、`< 8.4.27`、`< 8.5.12`, 使用旧版本的用户请尽快升级。

## 路径说明

- 工作目录: /app
- nginx.conf: /etc/nginx/nginx.conf
- php.ini: /usr/local/etc/php/php.ini
- php-fpm.conf: /usr/local/etc/php-fpm.d/zz-docker.conf

## 直接使用已构建好的镜像

```bash
docker pull edk24/docker-php:8.3
```

## 自行构建镜像

[github仓库](https://github.com/edk24/docker-php)
[buildx安装与常见问题](https://blog.csdn.net/u014110320/article/details/124406628)

```bash
# 构建镜像 (以 8.3 为例, 先进入对应版本目录)
$ cd 8.3
$ docker build --build-arg ENVIRONMENT=production -t edk24/docker-php:8.3 .

# 使用 buildx 构建多平台镜像并推送
$ docker buildx build --build-arg ENVIRONMENT=production -t edk24/docker-php:8.3 --platform linux/amd64,linux/arm/v6,linux/arm/v7,linux/arm64/v8,linux/386 --push .
```

也可以直接使用目录下的 `makefile`:

```bash
$ make build        # 生产镜像 edk24/docker-php:8.3
$ make build_dev    # 开发镜像 edk24/docker-php:8.3-dev
```

### 构建参数 (ARG)

| 参数名 | 默认值 | 取值范围 | 说明 |
| --- | --- | --- | --- |
| ENVIRONMENT | production | production, development | `development` 开发环境下会安装 `xdebug` 并配置 `9003` 为调试端口 |

## 快速运行

```bash
docker run --rm -it -v $(pwd):/app -w /app -p 3009:80 edk24/docker-php:8.3
```

访问: [http://localhost:3009](http://localhost:3009)

## 联系我

wechat: YmFzZTEwMjQ=
