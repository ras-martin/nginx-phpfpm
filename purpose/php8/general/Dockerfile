FROM --platform=${TARGETPLATFORM} alpine:3.16

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
        php8 \
        php8-fpm \
        php8-bcmath \
        php8-bz2 \
        php8-curl \
        php8-ctype \
        php8-dom \
        php8-exif \
        php8-fileinfo \
        php8-gd \
        php8-iconv \
        php8-intl \
        php8-mbstring \
        php8-mysqli \
        php8-pgsql \
        php8-opcache \
        php8-openssl \
        php8-pcntl \
        php8-pdo \
        php8-pdo_mysql \
        php8-pdo_pgsql \
        php8-phar \
        php8-posix \
        php8-tokenizer \
        php8-simplexml \
        php8-xml \
        php8-xmlreader \
        php8-xmlwriter \
        php8-zip \
        php8-pecl-xdebug \
        php8-pecl-oauth \
        php8-pecl-redis \
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
        /etc/php8/conf.d/zzz-override.ini \
        /application/log/error.log \
    && rm -f /etc/php8/conf.d/xdebug.ini \
    && adduser -u 82 -D -S -G www-data www-data \
    && chown -R www-data /application/log /application/run \
    && chown -R root:root /application/log/supervisord* \
    && chown www-data:www-data /application/sockets/php \
    && chown -R www-data /var/tmp/nginx

WORKDIR /application/src

# System stuff
COPY common/supervisord-php8.conf /etc/supervisord.conf
COPY common/nginx/nginx.conf /etc/nginx/nginx.conf
COPY purpose/php8/general/nginx/default.conf /etc/nginx/conf.d/default.conf
COPY purpose/php8/general/php8/php-cli.ini /etc/php8/php-cli.ini
COPY purpose/php8/general/php8/php.ini /etc/php8/php.ini
COPY common/php8/php-fpm.conf /etc/php8/php-fpm.conf
COPY common/php8/pool.conf /etc/php8/php-fpm.d/www.conf
COPY purpose/php8/general/php8/opcache.blacklist /etc/php8/conf.d/opcache.blacklist

# Entrypoint
COPY common/entrypoint/entrypoint.sh /
COPY common/entrypoint/entrypoint.d/ /entrypoint.d/
RUN chmod +x /entrypoint.d/*

EXPOSE 80

ENTRYPOINT ["/entrypoint.sh"]
CMD /usr/bin/supervisord -c /etc/supervisord.conf -n

