FROM --platform=${TARGETPLATFORM} alpine:3.19

ARG TARGETPLATFORM
ARG BUILD_DATE
LABEL maintainer="martin+docker@x8x.at" \
        arch="${TARGETPLATFORM}" \
        phpversion="${PHPVERSION}" \
        purpose="general" \
        builddate="${BUILD_DATE}"

ENV COMPOSER_HOME /tmp/composer
ENV COMPOSER_CACHE_DIR /tmp/composer/cache
ENV COMPOSER_ALLOW_SUPERUSER 1

RUN apk upgrade --update \
    && apk add --no-cache \
        bash \
        curl \
        git \
        nginx \
        supervisor \
        libseccomp \
        php83 \
        php83-fpm \
        php83-bcmath \
        php83-bz2 \
        php83-curl \
        php83-ctype \
        php83-dom \
        php83-exif \
        php83-fileinfo \
        php83-gd \
        php83-iconv \
        php83-intl \
        php83-mbstring \
        php83-mysqli \
        php83-pgsql \
        php83-opcache \
        php83-openssl \
        php83-pcntl \
        php83-pdo \
        php83-pdo_mysql \
        php83-pdo_pgsql \
        php83-phar \
        php83-posix \
        php83-tokenizer \
        php83-simplexml \
        php83-xml \
        php83-xmlreader \
        php83-xmlwriter \
        php83-zip \
        php83-pecl-xdebug \
        php83-pecl-redis \
        composer \
    && mkdir -p /run/nginx /var/tmp/nginx \
    && mkdir -p \
        /application \
        /application/src \
        /application/log \
        /application/log/nginx \
        /application/log/php \
        /application/log/supervisord \
        /application/run \
        /application/sockets \
        /application/sockets/php \
        /application/sockets/supervisord \
    && touch \
        /etc/php83/conf.d/zzz-override.ini \
        /application/log/error.log \
    && rm -f /etc/php83/conf.d/xdebug.ini \
    && adduser -u 82 -D -S -G www-data www-data \
    && chown -R www-data /application/log /application/run \
    && chown -R root:root /application/log/supervisord* \
    && chown www-data:www-data /application/sockets/php \
    && chown -R www-data /var/tmp/nginx \
    && chown -R www-data:www-data /var/lib/nginx

WORKDIR /application/src

# System stuff
COPY common/supervisord-php8.conf /etc/supervisord.conf
COPY common/nginx/nginx.conf /etc/nginx/nginx.conf
COPY purpose/php8/general/nginx/default.conf /etc/nginx/conf.d/default.conf
COPY purpose/php8/general/php8/php-cli.ini /etc/php83/php-cli.ini
COPY purpose/php8/general/php8/php.ini /etc/php83/php.ini
COPY common/php8/php-fpm.conf /etc/php83/php-fpm.conf
COPY common/php8/pool.conf /etc/php83/php-fpm.d/www.conf
COPY purpose/php8/general/php8/opcache.blacklist /etc/php83/conf.d/opcache.blacklist

# Entrypoint
COPY common/entrypoint/entrypoint.sh /
COPY common/entrypoint/entrypoint.d/ /entrypoint.d/
RUN chmod +x /entrypoint.d/*

EXPOSE 80

ENTRYPOINT ["/entrypoint.sh"]
CMD /usr/bin/supervisord -c /etc/supervisord.conf -n

