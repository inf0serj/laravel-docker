FROM php:5.6-fpm

# Update stretch repositories
RUN sed -i -e 's/deb.debian.org/archive.debian.org/g' \
           -e 's|security.debian.org|archive.debian.org/|g' \
           -e '/stretch-updates/d' /etc/apt/sources.list

RUN apt-get update  \
    && apt-get install -y \
        git \
        wget \
        libfreetype6-dev \
        libjpeg62-turbo-dev \
        libmcrypt-dev \
        libssl-dev \
        libmemcached-dev \
        libz-dev \
        zlib1g-dev \
        libsqlite3-dev \
        zip \
        libxml2-dev \
        libcurl3-dev \
        libedit-dev \
        libpspell-dev \
        libldap2-dev \
        unixodbc-dev \
        libpq-dev \
        libpng-dev \
        libgd2-xpm-dev

# https://bugs.php.net/bug.php?id=49876
RUN ln -fs /usr/lib/x86_64-linux-gnu/libldap.so /usr/lib/

# Install PHP extensions
RUN echo "Installing PHP extensions" \
    && docker-php-ext-install -j"$(nproc)"  \
      curl iconv opcache exif mcrypt gd intl mysqli  \
      pdo pdo_mysql pdo_pgsql pgsql pcntl zip bcmath  \
      simplexml xmlrpc soap pspell ldap mbstring \
    && docker-php-ext-configure gd --enable-gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
    && docker-php-ext-configure pgsql -with-pgsql=/usr/local/pgsql \
    && docker-php-ext-enable  \
      curl iconv opcache exif mcrypt gd intl mysqli  \
      pdo pdo_mysql pdo_pgsql pgsql pcntl zip bcmath  \
      simplexml xmlrpc soap pspell ldap mbstring

RUN wget --no-check-certificate https://pecl.php.net/get/redis-2.2.5.tgz \
    && pecl install --offline ./redis-2.2.5.tgz \
    && docker-php-ext-enable redis \
    && rm -rf redis-2.2.5.tgz

# Cleanup
RUN apt-get autoremove -y \
        && dpkg -la | awk '{print $2}' | grep '\-dev' | xargs apt-get remove -y \
        && apt-get clean all \
        && rm -rvf /var/lib/apt/lists/* \
        && rm -rvf /usr/share/doc /usr/share/man /usr/share/locale \
#        && rm -fv /usr/local/etc/php-fpm.d/*.conf \
        && rm -rvf /usr/src/php

WORKDIR /var/www/
