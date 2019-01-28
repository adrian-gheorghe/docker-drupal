ARG PHP_VERSION
FROM php:${PHP_VERSION}-apache

# Install dependencies
RUN apt-get update \
  && apt-get install -y \
    libfreetype6-dev \
    libicu-dev \
    libjpeg62-turbo-dev \
    libmcrypt-dev \
    libpng-dev \
    libxslt1-dev \
    sendmail-bin \
    sendmail \
    sudo \
    vim \
    cron \
    netcat \
    git \
    zip \
    && docker-php-ext-configure \
    gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/  && \
    a2enmod rewrite && \
    docker-php-ext-install \
    dom \
    gd \
    intl \
    mbstring \
    pdo_mysql \
    xsl \
    zip \
    soap \
    bcmath \
    opcache \
    mcrypt && \
    curl -sS https://getcomposer.org/installer | php && \
    mv composer.phar /usr/local/bin/composer && \
    { \
		echo 'opcache.memory_consumption=128'; \
		echo 'opcache.interned_strings_buffer=8'; \
		echo 'opcache.max_accelerated_files=4000'; \
		echo 'opcache.revalidate_freq=60'; \
		echo 'opcache.fast_shutdown=1'; \
		echo 'opcache.enable_cli=1'; \
	} > /usr/local/etc/php/conf.d/opcache-recommended.ini


RUN yes | pecl install xdebug \
    && echo "zend_extension=$(find /usr/local/lib/php/extensions/ -name xdebug.so)" > /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_enable=1" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_autostart=1" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_port=9000" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && touch /tmp/xdebug.log \
    && chmod 777 /tmp/xdebug.log \
    && echo "xdebug.remote_log=/tmp/xdebug.log" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_mode=req" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.remote_host=docker.host.internal" >> /usr/local/etc/php/conf.d/xdebug.ini \
    && echo "xdebug.max_nesting_level=1000" >> /usr/local/etc/php/conf.d/xdebug.ini

ARG DRUPAL_VERSION

ADD extra.ini /usr/local/etc/php/conf.d/extra.ini

RUN mkdir -p /var/www/.composer && \
    mkdir -p /var/www/drupal8 && \
    mkdir -p /var/www/html && \
    cd /var/www/drupal8 && \
    chown -R www-data:www-data /var/www

USER www-data

RUN composer create-project "drupal-composer/drupal-project:8.x-dev" /var/www/drupal8 --stability dev --no-interaction

RUN ls -al /var/www/drupal8 && \
    cat /var/www/drupal8/composer.json && \
    cd /var/www/drupal8 && \
    composer require drupal/core:"~$DRUPAL_VERSION" && \
    composer require --dev webflo/drupal-core-require-dev:"~$DRUPAL_VERSION" && \
    composer install && \
    ls -al /var/www/drupal8

USER root

WORKDIR /var/www/drupal8

RUN rm /etc/apache2/sites-enabled/000-default.conf

COPY vhost.conf /etc/apache2/sites-enabled/vhost.conf