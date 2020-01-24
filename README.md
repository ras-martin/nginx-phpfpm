# Docker-Image for Nginx & PHP-FPM stack based on Alpine Linux

* [Docker Hub](https://hub.docker.com/r/rasmartin/nginx-phpfpm)
* [GitHub](https://github.com/ras-martin/nginx-phpfpm)

This is a Docker-Image based on [Nginx](https://www.nginx.com/) and [PHP-FPM](https://www.php.net/). The different available tags offer a optimized configuration for some PHP-based applications like Nextcloud or Roundcube. Images are available for amd64 and arm (arm32v7, armhf) architectures.

## Available Tags

### General purpose

* `latest`, `general-amd64`
* `general-arm`

### ZF1

To host your [ZF1](https://github.com/zendframework/zf1)-based project you have to use [ZF1 with compatibility changes](https://github.com/Shardj/zf1-future) for PHP7, otherwise you can't run ZF1 on PHP7.

* `zf1-amd64`
* `zf1-arm`

## Environment variables
 * `FAKE_HTTPS`: possible values `on` or `off`, default `on`. Is used to tell the aplication it's running on https (-> `$_SERVER['HTTPS']`)
 * `DOCROOT_SUFFIX`: By default the docroot is set to `/application/src`. If required, the docroot path can be extended to `/applicaiton/src/$DOCROOT_SUFFIX`

## FAQ's

### Where do I have to mount the docroot to?

The docroot must be mounted to `/application/src`.

### How to overwrite PHP settings?

If it is required to overwrite PHP settings, create a custom configuration file ([php.ini](https://www.php.net/manual/en/ini.list.php) style) which contains the required configurations and mount it with `docker run` to `/etc/php7/conf.d/zzz-override.ini`.

### Why there is a PHP Opcache file blacklist?

To exclude PHP-files from being cached by PHP opcache.

For example: when you are using Nextcloud's `occ` (see [Nextcloud OCC](https://docs.nextcloud.com/server/15/admin_manual/configuration_server/occ_command.html)) it could happen that your Nextcloud `config/config.php` is modified, for example if you turning maintenance mode on/off with `occ maintenance:mode --[on|off]`. But if `config/config.php` is in Opcache, the maintenance mode setting wouldn't have any effect on your Nextcloud instance until your restart/reload the PHP-FPM (or reset the Opcache through other ways).

### Why PHP Xdebug is installed?

The extension is installed, but not active. Active it for development purposes by adding a `99_xdebug.ini` to `/etc/php7/conf.d` with your [Xdebug-config](https://xdebug.org/docs/all_settings), but at least with `zend_extension = xdebug.so`. 

