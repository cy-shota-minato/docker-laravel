FROM php:8.0-fpm-buster
LABEL maintainer="ucan-lab <yes@u-can.pro>"
SHELL ["/bin/bash", "-oeux", "pipefail", "-c"]

# timezone environment
ENV TZ=UTC \
  # locale
  LANG=en_US.UTF-8 \
  LANGUAGE=en_US:en \
  LC_ALL=en_US.UTF-8 \
  # composer environment
  COMPOSER_ALLOW_SUPERUSER=1 \
  COMPOSER_HOME=/composer

COPY --from=composer:2.0 /usr/bin/composer /usr/bin/composer

RUN apt-get update && \
  apt-get -y install git cron vim libicu-dev libonig-dev libzip-dev unzip locales git && \
  apt-get clean && \
  rm -rf /var/lib/apt/lists/* && \
  locale-gen en_US.UTF-8 && \
  localedef -f UTF-8 -i en_US en_US.UTF-8 && \
  mkdir /var/run/php-fpm && \
  git clone https://github.com/phpredis/phpredis.git /usr/src/php/ext/redis && \
  docker-php-ext-install intl pdo_mysql zip bcmath redis && \
  composer config -g process-timeout 3600 && \
  composer config -g repos.packagist composer https://packagist.org

COPY ./infra/docker/php/php-fpm.d/zzz-www.conf /usr/local/etc/php-fpm.d/zzz-www.conf
COPY ./infra/docker/php/php.ini /usr/local/etc/php/php.ini

# cron setting
COPY ./infra/docker/php/cron.d /etc/cron.d
RUN chmod -R 0644 /etc/cron.d
CMD cron -f

# 起動しないので起動
CMD php-fpm

WORKDIR /work/backend
