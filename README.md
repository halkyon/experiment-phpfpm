# Experimentation with PHP-FPM

# Why?

SilverStripe traditionally uses `mod_php` to serve PHP requests, so it was
interesting to see if PHP-FPM could be used instead to see if memory usage
could be reduced.

# Usage

First of all, clone this repo into a location in your filesystem somewhere.

In addition to the usual `composer` and `php` installed, please also
ensure `docker` and `docker-compose` are installed.

Now in the repository:

	composer install

Now you can choose which configuration to start below.

Once the containers have come up, you can access the site on http://localhost:8200

## Apache with PHP-FPM

This approach has the advantage in that all `.htaccess` files are used. The downside
is the overhead of reading these configuration files on each request, whether it is
PHP or a static file.

	docker-compose -f docker-compose-fpm-apache.yml up --build

This uses the [`httpd`](https://hub.docker.com/_/httpd/) Docker image.
Configuration for apache is located in `apache-fpm-template/httpd.conf`.

## nginx with PHP-FPM

This approach has the advantage in that `nginx` is very efficient in serving static assets.
The downside is `.htaccess` files are no longer used, so you'll need to write equivalent
nginx configuration.

	docker-compose -f docker-compose-fpm-nginx.yml up --build

This uses the [`nginx`](https://hub.docker.com/_/nginx/) Docker image.
Configuration for apache is located in `nginx-fpm-template/httpd.conf`.

## nginx with Apache mod_php

This is the "traditional" way of running PHP, in which PHP support is bundled into
Apache as a module. nginx is kept at the front to serve static assets, while offloading
anything else to Apache. `.htaccess` files are read on each PHP request, which doesn't make it
super efficient, combined with the fact that Apache must run in `mpm_prefork` to ensure
thread safety for PHP instead of the more efficient `mpm_event`.

	docker-compose -f docker-compose-modphp-nginx.yml up --build

This uses the [`php`](https://hub.docker.com/_/php/) Docker image.

Currently broken: port on apache is wrong and returns URLs as http://localhost
when forwarding docker container port to host on 8200.

# Further investigation

http://linuxbsdos.com/2015/02/17/how-to-reduce-php-fpm-php5-fpm-ram-usage-by-about-50/

https://ma.ttias.be/a-better-way-to-run-php-fpm/

# Testing

So far for each I have thrown `siege` at it to see how many transactions per second can be achieved.

Still to come: testing memory and CPU usage and comparing these.
