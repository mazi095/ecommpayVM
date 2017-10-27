FROM phusion/baseimage:latest

ENV DEBIAN_FRONTEND="noninteractive" \
    LANG=en_US.UTF-8 \
    LANGUAGE=en_US.UTF-8 \
    LC_ALL=en_US.UTF-8 \
    TERM=xterm \
    TZ=Europe/Moscow \
    NOTVISIBLE="in users profile" \
    CI_DATABASE_HOST=mysql

### Install packages from Debian repository and install php-extensions
RUN curl -o /usr/local/bin/gosu -L https://github.com/tianon/gosu/releases/download/1.9/gosu-amd64 \
    && chmod +x /usr/local/bin/gosu \
    && apt-add-repository -y ppa:ondrej/php \
    && apt-add-repository -y ppa:brightbox/ruby-ng \
    && apt-get update \
    && apt-get install -y --no-install-recommends git-core openssh-client vim nano less tree \
    && apt-get install -y \
        zsh \
        net-tools \
        iputils-ping \
        build-essential \
        ruby2.2 ruby2.2-dev \
        curl \
        mysql-client \
        php7.0-cli \
        php7.0-fpm \
        php7.0-common \
        php7.0-curl \
        php7.0-gd \
        php7.0-json \
        php7.0-mysql \
        php7.0-pgsql \
        php7.0-opcache \
        php7.0-readline \
        php7.0-sqlite3 \
        php7.0-intl \
        php7.0-bcmath \
        php7.0-zip \
        php7.0-xml \
        php7.0-mbstring \
        php7.0-soap \
        php-xdebug \
        php-mongodb \
   && rm -r /var/lib/apt/lists/* \
   && mkdir /run/php

### Install supervisor
RUN apt-get update && apt-get install -y supervisor \
    && mkdir -p  /var/log/supervisor

### Install Composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

ARG useruid

RUN groupadd --gid $useruid app && useradd --create-home --uid $useruid --gid $useruid --shell /bin/zsh app && mkdir /home/app/.ssh && touch /home/app/.ssh/config && chown -R $useruid:$useruid /home/app/.ssh
RUN cd /home/app && su -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)" app

WORKDIR /var/www

CMD ["/usr/bin/supervisord"]