ARG BASE_IMAGE_PREFIX
FROM ${BASE_IMAGE_PREFIX}alpine:3.13

ARG ARCH
ARG BUILD_DATE
LABEL maintainer="martin+docker@x8x.at" \
        arch="${ARCH}" \
        purpose="general" \
        builddate="${BUILD_DATE}"

COPY qemu-${ARCH}-static /usr/bin

ENV COMPOSER_HOME /tmp/composer
ENV COMPOSER_CACHE_DIR /tmp/composer/cache
ENV COMPOSER_ALLOW_SUPERUSER 1

RUN apk upgrade --update \
    && apk add --no-cache \
        bash \
        nginx \
        supervisor \
        composer \
        php8 \
        php8-fpm \
        php8-bcmath \
        php8-bz2 \
        php8-curl \
        php8-ctype \
        php8-dom \
        php8-fileinfo \
        php8-gd \
        php8-iconv \
        php8-intl \
        php8-mbstring \
        php8-mysqli \
        php8-pgsql \
        php8-opcache \
        php8-pcntl \
        php8-pdo \
        php8-pdo_mysql \
        php8-pdo_pgsql \
        php8-posix \
        php8-tokenizer \
        php8-simplexml \
        php8-xml \
        php8-zip \
        php8-pecl-xdebug \
        php8-pecl-oauth \
        php8-pecl-redis \
    && adduser -u 82 -D -S -G www-data www-data \
    && mkdir -p /run/nginx /var/tmp/nginx \
    && composer self-update \
    && composer global require hirak/prestissimo \
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
        /etc/php8/conf.d/zzz-override.ini \
        /application/log/error.log \
    && rm -f /etc/php8/conf.d/xdebug.ini \
    && chown -R www-data /application/log /application/run \
    && chown -R root:root /application/log/supervisord* \
    && chown www-data:www-data /application/sockets/php \
    && chown -R www-data /var/tmp/nginx

WORKDIR /application/src

# System stuff
COPY common/supervisord.conf /etc/supervisord.conf
COPY common/nginx/nginx.conf /etc/nginx/nginx.conf
COPY purpose/general/nginx/default.conf /etc/nginx/conf.d/default.conf
COPY purpose/general/php8/php-cli.ini /etc/php8/php-cli.ini
COPY purpose/general/php8/php.ini /etc/php8/php.ini
COPY common/php8/php-fpm.conf /etc/php8/php-fpm.conf
COPY common/php8/pool.conf /etc/php8/php-fpm.d/www.conf
COPY purpose/general/php8/opcache.blacklist /etc/php8/conf.d/opcache.blacklist

# Entrypoint
COPY common/entrypoint/entrypoint.sh /
COPY common/entrypoint/entrypoint.d/ /entrypoint.d/
RUN chmod +x /entrypoint.d/*

EXPOSE 80

ENTRYPOINT ["/entrypoint.sh"]
CMD /usr/bin/supervisord -c /etc/supervisord.conf -n

