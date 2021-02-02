FROM debian:buster
MAINTAINER styleflasher
LABEL php.version=7.4

# Base packages
# -----------------------------------------------------------------------------
RUN apt-get update && apt-get upgrade -y --no-install-recommends ; \
apt-get install -y gnupg2 -y ; \
apt install ca-certificates apt-transport-https wget unzip zip curl rsync ncdu make imagemagick -y ;

RUN wget -q https://packages.sury.org/php/apt.gpg -O- | apt-key add - ; \
echo "deb https://packages.sury.org/php/ buster main" | tee /etc/apt/sources.list.d/php.list ;  \
apt update -y && apt install -y \
    php7.4 -y \
    php7.4-cli \
    php7.4-common \
    php7.4-curl \
    php7.4-zip \
    php7.4-mbstring \
    php7.4-mysql \
    php7.4-xml \
    php7.4-soap \
    php7.4-gd \
    php7.4-imagick \
    php7.4-bcmath \
    php7.4-amqp \
    php7.4-mongodb \
    php7.4-dev \
    php7.4-xsl \
    php7.4-intl \
    php7.4-xdebug \
    php7.4-json \
    php7.4-apcu \
    php7.4-redis \
    libmcrypt-dev \
    php-pear

RUN pecl channel-update pecl.php.net

RUN pecl install channel://pecl.php.net/mcrypt-1.0.3

RUN echo '\
extension=mcrypt.so\n\
' >> /etc/php/7.4/cli/php.ini

RUN echo '\
extension=mcrypt.so\n\
' >> /etc/php/7.4/apache2/php.ini


# PHP config
# -----------------------------------------------------------------------------
# Custom PHP variables
RUN curl -L -o /etc/php/7.4/cli/conf.d/custom_vars.ini https://raw.githubusercontent.com/kaliop/docker-images/master/build_files/php/custom_vars.ini && \
    cp /etc/php/7.4/cli/conf.d/custom_vars.ini /etc/php/7.4/apache2/conf.d/custom_vars.ini



# Clear archives in apt cache folder
RUN apt-get clean && rm -rf /var/lib/apt/lists/*



ENTRYPOINT ["/bin/bash"]
