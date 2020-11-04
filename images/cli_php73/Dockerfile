FROM styleflasher/php73
MAINTAINER styleflasher

LABEL php.version=7.3 \
      application.type=ezpublish

ARG DOCKER_TIMEZONE=Europe/Vienna
ARG DOCKER_LOCALE=en_US.UTF-8

# Configure timezone
# -----------------------------------------------------------------------------
RUN echo $DOCKER_TIMEZONE > /etc/timezone; dpkg-reconfigure -f noninteractive tzdata

# Add Repo for MariaDB
#RUN sudo apt-key adv --recv-keys --keyserver keyserver.ubuntu.com 0xF1656F24C74CD1D8
#RUN sudo add-apt-repository 'deb [arch=amd64,i386] http://www.ftp.saix.net/DB/mariadb/repo/10.1/debian sid main'

# Base packages
# Java (used for eg. eZTika indexing)
# poppler-utils used for pdftotext binary (note that eztika is a much better option!)
# Mysql client, useful for db dumps and such
# @todo remove any unneeded ones (sudo, openssl?)
# -----------------------------------------------------------------------------
RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -y \
    curl \
    wget \
    telnet \
    git \
    pwgen \
    locales \
    sudo \
    cron \
    unzip \
    zip \
    pdftk \
    vim \
    nano \
    default-mysql-client \
    imagemagick \
    jpegoptim \
    bzip2 \
    poppler-utils \
    openjdk-11-jre-headless \
    php-zip \
    build-essential \
    ruby \
    ruby-dev \
    software-properties-common \
    msmtp \
    msmtp-mta;



# yarn global install
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-key add -
RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo tee /etc/apt/sources.list.d/yarn.list
RUN apt-get update && apt-get install -y yarn

# NVM
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.34.0/install.sh | bash

ENV NODE_PATH $NVM_DIR/versions/node/v$NODE_VERSION/lib/node_modules
ENV PATH      $NVM_DIR/versions/node/v$NODE_VERSION/bin:$PATH

# Node.js - https://nodejs.org/en/download/package-manager/#debian-and-ubuntu-based-linux-distributions
RUN curl -sL https://deb.nodesource.com/setup_14.x | sudo -E bash -
RUN apt-get install -y nodejs

# Configure locale for date, time & currency php functions
# q: for what is the locale setup needed ?
# -----------------------------------------------------------------------------
RUN dpkg-reconfigure locales && \
    echo $DOCKER_LOCALE' UTF-8'>> /etc/locale.gen && \
    locale-gen $DOCKER_LOCALE && \
    /usr/sbin/update-locale LANG=$DOCKER_LOCALE

ENV LC_ALL $DOCKER_LOCALE
ENV LANG $DOCKER_LOCALE
ENV LANGUAGE $DOCKER_LOCALE

# Local user
# @todo simplify this as much as possible
# -----------------------------------------------------------------------------
ADD profile /tmp/profile

# nb: the 1013 used here for user id and group id is later on replaced by the code in bootstrap.sh...
RUN addgroup --gid 1013 site && \
    adduser --system --uid=1013 --gid=1013 \
        --home /home/site --shell /bin/bash site && \
    adduser site sudo && \
    adduser site site && \
    sed -i '$ a site   ALL=\(ALL:ALL\) NOPASSWD: ALL' /etc/sudoers; \
    mkdir -p /home/site/.ssh; \
    curl https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash -o /home/site/.git-completion.bash; \
    curl -o /home/site/.git-prompt.sh -OL https://github.com/git/git/raw/master/contrib/completion/git-prompt.sh; \
    cp /etc/skel/.* /home/site/; \
    cat /tmp/profile/.bashrc_append >> /home/site/.bashrc; \
    cat /tmp/profile/.vimrc_append >> /home/site/.vimrc; \
    cp /tmp/profile/.gitconfig /home/site/; \
    chown -R site:site /home/site;

# Misc
# @todo simplify this as much as possible, or plain remove
# Disabled for now : may be needed for some ssh connections inside container
# -----------------------------------------------------------------------------
#RUN echo 'StrictHostKeyChecking no' >> /etc/ssh/ssh_config;


# Composer global install
# -----------------------------------------------------------------------------
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin && \
    mv /usr/local/bin/composer.phar /usr/local/bin/composer && \
    chmod 755 /usr/local/bin/composer && \
    mkdir -p /home/site/.composer && \
    chown -R site:site /home/site/.composer


# -----------------------------------------------------------------------------

# modman global install
RUN curl -s -L https://raw.github.com/colinmollenhour/modman/master/modman-installer | bash
RUN chmod +x ~/bin/modman
RUN mv ~/bin/modman /usr/local/bin/modman
# -----------------------------------------------------------------------------

# Clear archives in apt cache folder
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

COPY bootstrap.sh /root/bootstrap.sh
RUN chmod 755 /root/bootstrap.sh

# Install and enable IonCube loader
RUN curl https://downloads.ioncube.com/loader_downloads/ioncube_loaders_lin_x86-64.tar.gz -o /root/ioncube_loaders_lin_x86-64.tar.gz;
WORKDIR /root
RUN tar xf ioncube_loaders_lin_x86-64.tar.gz; \
    cp ioncube/ioncube_loader_lin_7.3.so /usr/lib/php/20180731/ioncube_loader_lin_7.3.so; \
    echo 'zend_extension = /usr/lib/php/20180731/ioncube_loader_lin_7.3.so' > /etc/php/7.3/apache2/conf.d/00-ioncube.ini; \
    echo 'zend_extension = /usr/lib/php/20180731/ioncube_loader_lin_7.3.so' > /etc/php/7.3/cli/conf.d/00-ioncube.ini;

WORKDIR /var/www/

CMD ["/root/bootstrap.sh"]
