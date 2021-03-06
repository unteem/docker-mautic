FROM php:5.6-fpm
MAINTAINER Michael Babker <michael.babker@mautic.org> (@mbabker)

# Install PHP extensions
RUN apt-get update && apt-get install -y libc-client-dev libicu-dev libkrb5-dev libmcrypt-dev libssl-dev unzip zip
RUN rm -rf /var/lib/apt/lists/*
RUN docker-php-ext-configure imap --with-imap --with-imap-ssl --with-kerberos
RUN docker-php-ext-install fcgi imap intl mbstring mcrypt mysqli pdo pdo_mysql zip

VOLUME /var/www/html

# Define Mautic version and expected SHA1 signature
ENV MAUTIC_VERSION 2.6.1
ENV MAUTIC_SHA1 686ea7443bb0a9cd80a3043b72dba725b2b05311

# Download package and extract to web volume
RUN curl -o mautic.zip -SL https://s3.amazonaws.com/mautic/releases/${MAUTIC_VERSION}.zip \
        && echo "$MAUTIC_SHA1 *mautic.zip" | sha1sum -c - \
        && mkdir /usr/src/mautic \
        && unzip mautic.zip -d /usr/src/mautic \
        && rm mautic.zip \
        && chown -R www-data:www-data /usr/src/mautic

# Copy init scripts and custom .htaccess
COPY docker-entrypoint.sh /entrypoint.sh
COPY makeconfig.php /makeconfig.php
COPY makedb.php /makedb.php
COPY php.ini-production /usr/local/etc/php/php.ini
COPY docker-php-mautic.ini /usr/local/etc/php/conf.d/docker-php-mautic.ini

ENTRYPOINT ["/entrypoint.sh"]
CMD ["php-fpm"]
