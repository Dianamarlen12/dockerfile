# dockerfile

docker pull ubuntu:20.04

FROM php:7.2-fpm
ARG HOSTUID
ENV BUILD_DEPS="autoconf file gcc g++ libc-dev make pkg-config re2c libfreetype6-dev libjpeg62-turbo-dev libmcrypt-dev libpng-dev libssl-dev libc-client-dev libkrb5-dev zlib1g-dev libicu-dev libldap-dev libxml2-dev libxslt-dev libcurl4-openssl-dev libpq-dev libsqlite3-dev" \
    ETC_DIR="/usr/local/etc" \
    ETC_BACKUP_DIR="/usr/local/etc_backup"

RUN apt-get update && apt-get install -y less \
    procps \
    git \
    && pecl install redis \
    && pecl install xdebug \
    && docker-php-ext-enable redis xdebug \
    && apt-get install -y $BUILD_DEPS \
    && docker-php-ext-install -j$(nproc) iconv \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-install -j$(nproc) gd \
    && docker-php-ext-configure imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-install -j$(nproc) imap \
    && docker-php-ext-install -j$(nproc) bcmath \
    && docker-php-ext-install -j$(nproc) calendar \
    && docker-php-ext-install -j$(nproc) exif \
    && docker-php-ext-install -j$(nproc) fileinfo \
    && docker-php-ext-install -j$(nproc) ftp \
    && docker-php-ext-install -j$(nproc) gettext \
    && docker-php-ext-install -j$(nproc) hash \
    && docker-php-ext-install -j$(nproc) intl \
    && docker-php-ext-install -j$(nproc) json \
    && docker-php-ext-install -j$(nproc) ldap \
    && docker-php-ext-install -j$(nproc) sysvshm \
    && docker-php-ext-install -j$(nproc) sysvsem \
    && docker-php-ext-install -j$(nproc) xml \
    && docker-php-ext-install -j$(nproc) zip \
    && docker-php-ext-install -j$(nproc) xsl \
    && docker-php-ext-install -j$(nproc) phar \
    && docker-php-ext-install -j$(nproc) ctype \
    && docker-php-ext-install -j$(nproc) curl \
    && docker-php-ext-install -j$(nproc) dom \
    && docker-php-ext-install -j$(nproc) soap \
    && docker-php-ext-install -j$(nproc) mbstring \
    && docker-php-ext-install -j$(nproc) posix \
    && docker-php-ext-install -j$(nproc) pdo_pgsql \
    && docker-php-ext-install -j$(nproc) pdo_sqlite \
    && docker-php-ext-install -j$(nproc) pdo_mysql \
    && yes | pecl install "channel://pecl.php.net/mcrypt-1.0.1" \
    && { \
    echo 'extension=mcrypt.so'; \
    } > $PHP_INI_DIR/conf.d/pecl-mcrypt.ini \
    && echo "Fin de instalaciones"
COPY docker-entry.sh /usr/local/binx

RUN mv $ETC_DIR $ETC_BACKUP_DIR \
    && chmod +x /usr/local/bin/docker-entry.sh \
    && rm /etc/localtime

RUN useradd -s /bin/bash -d /var/www -u $HOSTUID user

ENTRYPOINT ["/usr/local/bin/docker-entry.sh"]
CMD ["php-fpm"]