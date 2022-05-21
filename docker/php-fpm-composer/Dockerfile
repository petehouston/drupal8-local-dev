FROM php:7.4.29-fpm
LABEL maintainer="contact@petehouston.com"

RUN apt-get update && apt-get install -y --fix-missing \
    default-mysql-client \
    imagemagick \
    graphviz \
    git \
    libfreetype6-dev \
    libpng-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libxml2-dev \
    libxslt1-dev \
    wget \
    linux-libc-dev \
    libyaml-dev \
    libzip-dev \
    libicu-dev \
    libpq-dev \
    libonig-dev \
    libssl-dev && \
    rm -r /var/lib/apt/lists/*

RUN docker-php-ext-configure gd --with-freetype --with-jpeg

RUN docker-php-ext-install \
    mysqli \
    pdo_mysql \
    gd \
    mbstring \
    xsl \
    opcache \
    calendar \
    intl \
    exif \
    ftp \
    bcmath \
    zip

RUN wget https://getcomposer.org/download/1.10.26/composer.phar -O composer && mv composer /usr/bin/composer && chmod +x /usr/bin/composer

ADD xdebug.ini  /etc/php7.4/conf.d/

RUN echo "upload_max_filesize = 500M\n" \
         "post_max_size = 500M\n" \
         > /usr/local/etc/php/conf.d/maxsize.ini

RUN mkdir -p $HOME/.composer

RUN chown -R www-data:www-data $HOME/.composer/

USER www-data
WORKDIR /var/www/html
VOLUME /var/www/html
