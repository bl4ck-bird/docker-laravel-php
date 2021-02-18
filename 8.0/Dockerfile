ARG PHP_VERSION

FROM php:${PHP_VERSION}-fpm-alpine

LABEL maintainer="bl4ckbird <bl4ckbird@bl4ckbird.com>"

# Install tzdata.
RUN apk add --no-cache tzdata
ENV TZ="UTC"

# Install extensions.
RUN cd /; \
    \
# Install dependency packages.
    apk add --no-cache --virtual .build-deps $PHPIZE_DEPS libzip-dev zstd-dev lz4-dev imagemagick6-dev; \
    docker-php-source extract; \
    \
# Install Zip, Opcache, BCMath, PDO_MYSQL extension.
    docker-php-ext-install -j$(nproc) zip opcache bcmath pdo_mysql; \
    docker-php-ext-enable zip opcache bcmath pdo_mysql; \
    \
# Install igbinary extension.
    pecl bundle -d /usr/src/php/ext igbinary; \
    docker-php-ext-install -j$(nproc) igbinary; \
    docker-php-ext-enable igbinary; \
    \
# Install ImageMagick extension.
    mkdir -p /usr/src/php/ext/imagick; \
    curl -sL0 https://github.com/Imagick/imagick/archive/448c1cd0d58ba2838b9b6dff71c9b7e70a401b90.tar.gz \
        | tar xz -C "/usr/src/php/ext/imagick" --strip 1; \
    docker-php-ext-configure imagick; \
    docker-php-ext-install -j$(nproc) imagick; \
    docker-php-ext-enable imagick; \
    \
# Install redis extension.
    pecl bundle -d /usr/src/php/ext redis; \
    docker-php-ext-configure redis --enable-redis-igbinary --enable-redis-lzf --enable-redis-zstd --enable-redis-lz4 --with-liblz4=/usr/lib; \
    docker-php-ext-install -j$(nproc) redis; \
    docker-php-ext-enable redis; \
    \
# Install xdebug extension.
    pecl bundle -d /usr/src/php/ext xdebug; \
    docker-php-ext-configure xdebug; \
    docker-php-ext-install -j$(nproc) xdebug; \
    docker-php-ext-enable xdebug; \
    \
# Remove not used dependency packages.
    docker-php-source delete; \
    \
    runDeps="$( \
        scanelf --needed --nobanner --format '%n#p' --recursive /usr/local \
            | tr ',' '\n' \
            | sort -u \
            | awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
    )"; \
    apk add --no-cache $runDeps; \
    \
    apk del --no-network .build-deps; \
    \
    rm -rf /tmp/pear ~/.pear;

# Install composer.
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Copy php, php-fpm confiure files.
COPY php.ini $PHP_INI_DIR/
COPY www.conf /usr/local/etc/php-fpm.d/

ENV \
# PHP configure.
    PHP_DATE_TIMEZONE="$TZ" \
    PHP_EXPOSE_PHP="On" \
    PHP_SHORT_OPEN_TAG="Off" \
    PHP_ALLOW_URL_FOPEN="Off" \
    PHP_MAX_EXECUTION_TIME="60" \
    PHP_MEMORY_LIMIT="256M" \
    PHP_POST_MAX_SIZE="100M" \
    PHP_UPLOAD_MAX_FILESIZE="100M" \
    \
# PHP-FPM configure.
    PHP_FPM_PM="dynamic" \
    PHP_FPM_PM_MAX_CHILDREN="5" \
    PHP_FPM_PM_START_SERVERS="2" \
    PHP_FPM_PM_MIN_SPARE_SERVERS="1" \
    PHP_FPM_PM_MAX_SPARE_SERVERS="3" \
    PHP_FPM_PM_PROCESS_IDLE_TIMEOUT="10s" \
    PHP_FPM_PM_MAX_REQUESTS="0" \
    PHP_FPM_CLEAR_ENV="1" \
    \
# Xdebug configure.
    PHP_XDEBUG_MODE="develop" \
    PHP_XDEBUG_CLIENT_HOST="host.docker.internal" \
    PHP_XDEBUG_CLIENT_PORT="9003" \
    PHP_XDEBUG_START_UPON_ERROR="yes" \
    \
# OPCache configure.
# See https://www.php.net/manual/en/opcache.configuration.php
    PHP_OPCACHE_ENABLE="On" \
    PHP_OPCACHE_ENABLE_CLI="Off" \
    PHP_OPCACHE_MEMORY_CONSUMPTION="256" \
    PHP_OPCACHE_INTERNED_STRINGS_BUFFER="16" \
    PHP_OPCACHE_MAX_ACCELERATED_FILES="16229" \
    PHP_OPCACHE_MAX_WASTED_PERCENTAGE="10" \
    PHP_OPCACHE_VALIDATE_TIMESTAMPS="On" \
    PHP_OPCACHE_REVALIDATE_FREQ="60" \
    PHP_OPCACH_JIT="tracing" \
    PHP_OPCACH_JIT_BUFFER_SIZE="50M"
