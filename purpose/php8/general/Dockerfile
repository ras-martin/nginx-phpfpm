FROM --platform=${TARGETPLATFORM} alpine:3.17

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
        php81 \
        php81-fpm \
        php81-bcmath \
        php81-bz2 \
        php81-curl \
        php81-ctype \
        php81-dom \
        php81-exif \
        php81-fileinfo \
        php81-gd \
        php81-iconv \
        php81-intl \
        php81-mbstring \
        php81-mysqli \
        php81-pgsql \
        php81-opcache \
        php81-openssl \
        php81-pcntl \
        php81-pdo \
        php81-pdo_mysql \
        php81-pdo_pgsql \
        php81-phar \
        php81-posix \
        php81-tokenizer \
        php81-simplexml \
        php81-xml \
        php81-xmlreader \
        php81-xmlwriter \
        php81-zip \
        php81-pecl-xdebug \
        php81-pecl-redis \
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
    && chown -R www-data /var/tmp/nginx \
    && chown -R www-data:www-data /var/lib/nginx

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

