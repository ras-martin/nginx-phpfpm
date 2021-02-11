ARG BASE_IMAGE_PREFIX
FROM ${BASE_IMAGE_PREFIX}alpine:3.13

ARG ARCH
ARG BUILD_DATE
LABEL maintainer="martin+docker@x8x.at" \
        arch="${ARCH}" \
        phpversion="${PHPVERSION}" \
        purpose="zf1" \
        builddate="${BUILD_DATE}"

COPY qemu-${ARCH}-static /usr/bin

ENV COMPOSER_HOME /tmp/composer
ENV COMPOSER_CACHE_DIR /tmp/composer/cache
ENV COMPOSER_ALLOW_SUPERUSER 1

ENV DOCROOT_SUFFIX public

RUN apk upgrade --update \
    && apk add --no-cache \
        bash \
        nginx \
        supervisor \
        composer \
        php7 \
        php7-fpm \
        php7-bcmath \
        php7-bz2 \
        php7-curl \
        php7-ctype \
        php7-dom \
        php7-gd \
        php7-iconv \
        php7-intl \
        php7-mbstring \
        php7-mysqli \
        php7-opcache \
        php7-pcntl \
        php7-pdo \
        php7-pdo_mysql \
        php7-posix \
        php7-tokenizer \
        php7-simplexml \
        php7-xml \
        php7-zip \
        php7-pecl-xdebug \
        php7-pecl-oauth \
        php7-pecl-redis \
    && adduser -u 82 -D -S -G www-data www-data \
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
        /etc/php7/conf.d/zzz-override.ini \
        /application/log/error.log \
    && rm -f /etc/php7/conf.d/xdebug.ini \
    && chown -R www-data /application/log /application/run \
    && chown -R root:root /application/log/supervisord* \
    && chown www-data:www-data /application/sockets/php \
    && chown -R www-data /var/tmp/nginx

WORKDIR /application/src

# System stuff
COPY common/supervisord.conf /etc/supervisord.conf
COPY common/nginx/nginx.conf /etc/nginx/nginx.conf
COPY purpose/php7/zf1/nginx/default.conf /etc/nginx/conf.d/default.conf
COPY purpose/php7/zf1/php7/php.ini /etc/php7/php.ini
COPY common/php7/php-fpm.conf /etc/php7/php-fpm.conf
COPY common/php7/pool.conf /etc/php7/php-fpm.d/www.conf
COPY purpose/php7/zf1/php7/opcache.blacklist /etc/php7/conf.d/opcache.blacklist

# Entrypoint
COPY common/entrypoint/entrypoint.sh /
COPY common/entrypoint/entrypoint.d/ /entrypoint.d/
RUN chmod +x /entrypoint.d/*

EXPOSE 80

ENTRYPOINT ["/entrypoint.sh"]
CMD /usr/bin/supervisord -c /etc/supervisord.conf -n

