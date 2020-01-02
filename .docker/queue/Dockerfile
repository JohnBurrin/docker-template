FROM php:7.3.2-cli

ARG debug

# mysql dep for app
RUN docker-php-ext-install -j$(nproc) pdo_mysql

# process deps for queue daemon
RUN docker-php-ext-install -j$(nproc) bcmath
RUN docker-php-ext-install -j$(nproc) pcntl
RUN docker-php-ext-install -j$(nproc) sockets

# handle images with PHP
RUN apt-get update && apt-get install -y libpng-dev libjpeg-dev \
    && docker-php-ext-configure gd \
    --with-jpeg-dir=/usr/lib/ \
    --with-gd \
    && docker-php-ext-install -j $(nproc) gd exif

# configure apache as late as possible to avoid unnecessary cache invalidations
RUN ln -sf /usr/share/zoneinfo/Europe/London /etc/localtime \
    && echo "Europe/London" > /etc/timezone \
    && echo 'date.timezone=Europe/London' > /usr/local/etc/php/conf.d/settimezone.ini

# grab composer
RUN apt-get update \
    && apt-get install -y git zip unzip
RUN curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer
RUN composer global require hirak/prestissimo

WORKDIR /var/www

# install composer dependencies
COPY composer.json composer.lock ./
RUN if [ $debug ]; then \
	composer install --no-scripts; \
else \
	composer install --no-scripts --no-dev; \
fi
# copy rest of fileset
COPY . .
