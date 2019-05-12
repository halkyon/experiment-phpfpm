FROM php:7.3-fpm

RUN apt-get update && apt-get install -y \
	libfreetype6-dev \
	libjpeg62-turbo-dev \
	libpng-dev \
	zlib1g-dev \
	libzip-dev \
	libicu-dev \
	g++ \
		&& docker-php-ext-configure intl \
		&& docker-php-ext-configure mysqli --with-mysqli=mysqlnd \
		&& docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ \
		&& docker-php-ext-install -j$(nproc) gd intl opcache mysqli pdo pdo_mysql zip
