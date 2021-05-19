FROM php:7.4-fpm-alpine

# Install phpize
RUN apk add --no-cache --virtual .build-deps $PHPIZE_DEPS && \
    apk add --no-cache freetype-dev libjpeg-turbo-dev libpng-dev libzip-dev bzip2-dev && \
    docker-php-ext-configure gd --with-freetype --with-jpeg && \
    # Install gd zip bz2 bcmath pdo_mysql opcache
    docker-php-ext-install -j$(nproc) gd zip bz2 bcmath pdo_mysql opcache && \
    # Install redis
    pecl install redis && docker-php-ext-enable redis && \
    # Remove useless package
    apk del --no-network .build-deps

# Install composer globally
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/bin/ --filename=composer

# PHP ini default
ENV PHP_DATE_TIMEZONE Asia/Taipei
ENV PHP_MEMORY_LIMIT 256M
ENV PHP_POST_MAX_SIZE 10M
ENV PHP_UPLOAD_MAX_FILESIZE 10M
ENV PHP_MAX_FILE_UPLOADS 20
ENV PHP_SESSION_SAVE_HANDLER files
ENV PHP_SESSION_SAVE_PATH /var/lib/php/sessions
ENV PHP_SESSION_COOKIE_HTTPONLY 1

# OPcache defaults
ENV PHP_OPCACHE_ENABLE 1
ENV PHP_OPCACHE_MEMORY_CONSUMPTION 128
ENV PHP_OPCACHE_MAX_ACCELERATED_FILES 10000
ENV PHP_OPCACHE_REVALIDATE_FREQUENCY 0
ENV PHP_OPCACHE_VALIDATE_TIMESTAMPS 0

# PHP-FPM defaults
ENV PHP_FPM_PM dynamic
ENV PHP_FPM_MAX_CHILDREN 600
ENV PHP_FPM_START_SERVERS 40
ENV PHP_FPM_MIN_SPARE_SERVERS 20
ENV PHP_FPM_MAX_SPARE_SERVERS 50
ENV PHP_FPM_MAX_REQUESTS 500

# Use the default production configuration
RUN mv "${PHP_INI_DIR}/php.ini-production" "${PHP_INI_DIR}/php.ini"

# Copy the Default configuration file
COPY image-files/php/conf.d/php.ini "${PHP_INI_DIR}/conf.d/php.ini"

# Copy the OPcache configuration file
COPY image-files/php/conf.d/opcache.ini "${PHP_INI_DIR}/conf.d/opcache.ini"

# Copy the PHP-FPM configuration file
COPY image-files/php-fpm.d/www.conf /usr/local/etc/php-fpm.d/www.conf

# Application root path
ENV APP_DIR /srv/app
WORKDIR /srv/app