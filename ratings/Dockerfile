FROM php:7.4-apache

# Install required dependencies
RUN apt-get update && apt-get install -yqq unzip libzip-dev \
    && docker-php-ext-install pdo_mysql opcache zip

# Enable AutoProfile for PHP which is currently opt-in beta
RUN echo "instana.enable_auto_profile=1" > "/usr/local/etc/php/conf.d/zzz-instana-extras.ini"

# Configure Apache
COPY status.conf /etc/apache2/mods-available/status.conf
RUN a2enmod rewrite && a2enmod status

WORKDIR /var/www/html

# Copy application files
COPY html/ /var/www/html

# Copy Composer from a multi-stage build
COPY --from=composer /usr/bin/composer /usr/bin/composer

# Retry composer install in case of failure and use --prefer-dist
RUN composer install --prefer-dist || composer install --prefer-dist

# Set correct permissions
RUN rm -Rf /var/www/var/*
RUN chown -R www-data /var/www
RUN chmod -R 777 /var/www
