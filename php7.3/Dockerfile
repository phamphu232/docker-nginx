FROM php:7.3-fpm

ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

RUN chmod +x /usr/local/bin/install-php-extensions && \
    install-php-extensions \
    bcmath \
    bz2 \
    calendar \
    ctype \
    curl \
    date \
    dba \
    dom \
    enchant \
    exif \
    fileinfo \
    filter \
    ftp \
    gd \
    gettext \
    gmp \
    hash \
    iconv \
    igbinary \
    imap \
    intl \
    json \
    ldap \
    mbstring \
    mcrypt \
    memcached \
    msgpack \
    mysqli \
    mysqlnd \
    odbc \
    openssl \
    pcntl \
    pcre \
    PDO \
    pdo_dblib \
    pdo_mysql \
    PDO_ODBC \
    pdo_sqlite \
    Phar \
    posix \
    pspell \
    readline \
    recode \
    Reflection \
    session \
    shmop \
    SimpleXML \
    snmp \
    soap \
    sockets \
    sodium \
    SPL \
    sqlite3 \
    ssh2 \
    standard \
    sysvmsg \
    sysvsem \
    sysvshm \
    tidy \
    tokenizer \
    wddx \
    xml \
    xmlreader \
    xmlrpc \
    xmlwriter \
    xsl \
    zip

# Install Redis extension
RUN pecl install redis && docker-php-ext-enable redis

# Enable Zend OPcache
RUN docker-php-ext-enable opcache

# copy the Composer PHAR from the Composer image into the PHP image
COPY --from=composer:2.3 /usr/bin/composer /usr/bin/composer

# Expose the port that PHP-FPM will run on
EXPOSE 9000

# CMD to start PHP-FPM
CMD ["php-fpm"]