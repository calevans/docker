FROM php:7.3

ENV XDEBUG_VERSION 2.7.1

RUN additionalPackages=" \
        apt-transport-https \
        git \
        msmtp-mta \
        openssh-client \
        rsync \
        gnupg2 \
        unzip \
        zip \
    " \
    buildDeps=" \
        freetds-dev \
        libbz2-dev \
        libc-client-dev \
        libenchant-dev \
        libfreetype6-dev \
        libgmp3-dev \
        libjpeg62-turbo-dev \
        libkrb5-dev \
        libldap2-dev \
        libpng-dev \
        libpq-dev \
        libpspell-dev \
        librabbitmq-dev \
        libsasl2-dev \
        libsnmp-dev \
        libssl-dev \
        libtidy-dev \
        libxml2-dev \
        libxpm-dev \
        libxslt1-dev \
    " \
    && runDeps=" \
        libc-client2007e \
        libenchant1c2a \
        libfreetype6 \
        libjpeg62-turbo \
        libpq5 \
        libsybdb5 \
        libtidy-dev \
        libx11-6 \
        libxpm4 \
        libxslt1.1 \
        snmp \
    " \
    && phpModules=" \
        bcmath \
        bz2 \
        calendar \
        dba \
        enchant \
        exif \
        ftp \
        gd \
        gettext \
        gmp \
        imap \
        intl \
        ldap \
        mbstring \
        mysqli \
        opcache \
        pcntl \
        pdo \
        pdo_dblib \
        pdo_mysql \
        pdo_pgsql \
        pgsql \
        pspell \
        shmop \
        snmp \
        soap \
        sockets \
        sysvmsg \
        sysvsem \
        sysvshm \
        tidy \
        wddx \
        xmlrpc \
        xsl \
        xdebug \
    " \
    && echo "deb http://httpredir.debian.org/debian jessie contrib non-free" > /etc/apt/sources.list.d/additional.list \
    && apt-get update \
    && apt-get install -y --no-install-recommends apt-utils \
    && apt-get install -y --no-install-recommends $additionalPackages $buildDeps $runDeps \
    && docker-php-source extract \
    && cd /usr/src/php/ext/ \
    && curl -L http://xdebug.org/files/xdebug-$XDEBUG_VERSION.tgz | tar -zxf - \
    && mv xdebug-$XDEBUG_VERSION xdebug \
    && ln -s /usr/include/x86_64-linux-gnu/gmp.h /usr/include/gmp.h \
    && ln -s /usr/lib/x86_64-linux-gnu/libldap_r.so /usr/lib/libldap.so \
    && ln -s /usr/lib/x86_64-linux-gnu/libldap_r.a /usr/lib/libldap_r.a \
    && ln -s /usr/lib/x86_64-linux-gnu/libsybdb.a /usr/lib/libsybdb.a \
    && ln -s /usr/lib/x86_64-linux-gnu/libsybdb.so /usr/lib/libsybdb.so \
    && docker-php-ext-configure gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-xpm-dir=/usr/include/ \
    && docker-php-ext-configure imap --with-imap --with-kerberos --with-imap-ssl \
    && docker-php-ext-configure ldap --with-ldap-sasl \
    && docker-php-ext-install $phpModules \
    && printf "\n" | pecl install amqp \
    && pecl install igbinary \
#    && printf "\n" | pecl install memcache \
    && pecl install mongodb \
    && pecl install redis \
    && for ext in $phpModules; do \
           rm -f /usr/local/etc/php/conf.d/docker-php-ext-$ext.ini; \
       done \
    && docker-php-source delete \
    && apt-get install -y libzip-dev zlib1g-dev \
    && docker-php-ext-install zip \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false -o APT::AutoRemove::SuggestsImportant=false $buildDeps \
    && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Install composer and put binary into $PATH
RUN curl -sS https://getcomposer.org/installer | php \
    && mv composer.phar /usr/local/bin/ \
    && ln -s /usr/local/bin/composer.phar /usr/local/bin/composer

# Install Node.js & Yarn
RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - \
    && echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list \
    && curl -sL https://deb.nodesource.com/setup_8.x | bash - \
    && apt-get install -y nodejs build-essential yarn \
    && apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY msmtprc /etc/
COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT ["/entrypoint.sh"]
CMD ["php", "-a"]
