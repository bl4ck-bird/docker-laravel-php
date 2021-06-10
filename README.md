# PHP-FPM Docker image for Laravel

![build](https://github.com/bl4ck-bird/docker-laravel-php-fpm/workflows/build/badge.svg?branch=master&event=workflow_dispatch)

Docker image for a php-fpm container crafted to run Laravel based applications.

## Differences from the official php docker image

- Install php extensions: `zip`, `opcache`, `bcmath`, `pdo_mysql`, `igbinary`, `imagick`, `redis`, `pcntl`, `sockets`, `swoole`, `xdebug`.
- Add `composer`.

## Usage

### Install dependency packages.

1. Run docker command.
    ```bash
    docker run --rm -v $(pwd):/var/www/html flrnrud7/laravel-php-fpm:latest composer install
    ```
2. Run docker-compose command.
    ```bash
    docker-compose run --rm php-fpm composer install
    ```

### Start with docker-compose

```yml
version: '3'

services:
  nginx:
    image: flrnrud7/laravel-nginx:latest
    ports:
      - "80:80"
    depends_on:
      - php-fpm
    volumes:
      - .:/usr/share/nginx/html:cached

  php-fpm:
    image: flrnrud7/laravel-php-fpm:latest
    volumes:
      - .:/var/www/html:cached
```

## Environment Variables

| Name | Description | Default value |
| ---- | ----------- | ------- |
| `TZ` | Alpine OS timezone | `UTC` |
| `PHP_DATE_TIMEZONE` | `date.timezone` on `php.ini` | `TZ` enviroment variable value |
| `PHP_EXPOSE_PHP` | `expose_php` on `php.ini` | `On` |
| `PHP_SHORT_OPEN_TAG` | `short_open_tag` on `php.ini` | `Off` |
| `PHP_ALLOW_URL_FOPEN` | `allow_url_fopen` on `php.ini` | `Off` |
| `PHP_MAX_EXECUTION_TIME` | `max_execution_time` on `php.ini` | `60` |
| `PHP_MEMORY_LIMIT` | `memory_limit` on `php.ini` | `256M` |
| `PHP_POST_MAX_SIZE` | `post_max_size` on `php.ini` | `100M` |
| `PHP_UPLOAD_MAX_FILESIZE` | `upload_max_filesize` on `php.ini` | `100M` |
| `PHP_FPM_PM` | `pm` on `php-fpm.d/www.conf` | `dynamic` |
| `PHP_FPM_PM_MAX_CHILDREN` | `pm.max_children` on `php-fpm.d/www.conf` | `5` |
| `PHP_FPM_PM_START_SERVERS` | `pm.start_servers` on `php-fpm.d/www.conf` | `2` |
| `PHP_FPM_PM_MIN_SPARE_SERVERS` | `pm.min_spare_servers` on `php-fpm.d/www.conf` | `1` |
| `PHP_FPM_PM_MAX_SPARE_SERVERS` | `pm.max_spare_servers` on `php-fpm.d/www.conf` | `3` |
| `PHP_FPM_PM_PROCESS_IDLE_TIMEOUT` | `pm.process_idle_timeout` on `php-fpm.d/www.conf` | `10s` |
| `PHP_FPM_PM_MAX_REQUESTS` | `pm.max_requests` on `php-fpm.d/www.conf` | `0` |
| `PHP_FPM_CLEAR_ENV` | `clear_env` on `php-fpm.d/www.conf` | `1` |
| `PHP_XDEBUG_MODE` | `xdebug.mode` on `php.ini` | `off` |
| `PHP_XDEBUG_CLIENT_HOST` | `xdebug.client_host` on `php.ini` | `host.docker.internal` |
| `PHP_XDEBUG_CLIENT_PORT` | `xdebug.client_port` on `php.ini` | `9003` |
| `PHP_XDEBUG_START_UPON_ERROR` | `xdebug.start_upon_error` on `php.ini` | `yes` |
| `PHP_OPCACHE_ENABLE` | `opcache.enable` on `php.ini` | `On` |
| `PHP_OPCACHE_ENABLE_CLI` | `opcache.enable_cli` on `php.ini` | `Off` |
| `PHP_OPCACHE_MEMORY_CONSUMPTION` | `opcache.memory_consumption` on `php.ini` | `256` |
| `PHP_OPCACHE_INTERNED_STRINGS_BUFFER` | `opcache.interned_strings_buffer` on `php.ini` | `16` |
| `PHP_OPCACHE_MAX_ACCELERATED_FILES` | `opcache.max_accelerated_files` on `php.ini` | `16229` |
| `PHP_OPCACHE_MAX_WASTED_PERCENTAGE` | `opcache.max_wasted_percentage` on `php.ini` | `10` |
| `PHP_OPCACHE_VALIDATE_TIMESTAMPS` | `opcache.validate_timestamps` on `php.ini` | `On` |
| `PHP_OPCACHE_REVALIDATE_FREQ` | `opcache.revalidate_freq` on `php.ini` | `60` |
| `PHP_OPCACH_JIT` | `opcach.jit` on `php.ini` | `tracing` |
| `PHP_OPCACH_JIT_BUFFER_SIZE` | `opcach.jit_buffer_size` on `php.ini` | `50M` |
