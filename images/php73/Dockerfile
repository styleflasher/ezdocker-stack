FROM debian:buster
MAINTAINER styleflasher
LABEL php.version=7.3

# Base packages
# -----------------------------------------------------------------------------
RUN apt-get update && \
    apt-get install -y apt-transport-https ca-certificates wget lsb-release

# PHP packages
# -----------------------------------------------------------------------------
RUN apt-get update && \
    apt-get install -y \
    curl \
    rsync \
    ncdu \
    php \
    php-common \
    php-readline \
    php-mysql \
    php-json \
    php-xsl \
    php-intl \
    php-curl \
    php-imagick \
    php-gd \
    php-xmlrpc \
    php-memcached \
    php-xdebug \
    php-mbstring \
    php-bcmath \
    php-soap \
    php-zip \
    php-apcu \
    php-redis \
    php-dev \
    libmcrypt-dev \
    php-pear

RUN pecl channel-update pecl.php.net

RUN pecl install channel://pecl.php.net/mcrypt-1.0.2

RUN echo '\
extension=mcrypt.so\n\
' >> /etc/php/7.3/cli/php.ini

RUN echo '\
extension=mcrypt.so\n\
' >> /etc/php/7.3/apache2/php.ini
    

# PHP config
# -----------------------------------------------------------------------------
# Custom PHP variables
RUN curl -L -o /etc/php/7.3/cli/conf.d/custom_vars.ini https://raw.githubusercontent.com/kaliop/docker-images/master/build_files/php/custom_vars.ini && \
    cp /etc/php/7.3/cli/conf.d/custom_vars.ini /etc/php/7.3/apache2/conf.d/custom_vars.ini


# Clear archives in apt cache folder
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

ENTRYPOINT ["/bin/bash"]
