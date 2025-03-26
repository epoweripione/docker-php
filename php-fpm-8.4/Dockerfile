FROM php:8.4-fpm

LABEL Maintainer="Ansley Leung" \
      Description="latest PHP8 fpm Docker image. Use `docker-php-ext-install extension_name` to install Extensions." \
      License="MIT License" \
      Version="8.4.5"

# Environments
ENV TZ=Asia/Shanghai
ENV COMPOSER_ALLOW_SUPERUSER=1
ENV COMPOSER_HOME=/usr/local/share/composer

# Install extensions
# ref to: https://github.com/mlocati/docker-php-extension-installer
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/

RUN chmod +x /usr/local/bin/install-php-extensions && sync && \
    install-php-extensions \
        bcmath \
        bz2 \
        calendar \
        csv \
        decimal \
        exif \
        gd \
        geospatial \
        gettext \
        gmp \
        gnupg \
        igbinary \
        imagick \
        imap \
        intl \
        json_post \
        ldap \
        mailparse \
        memcache \
        memcached \
        mongodb \
        msgpack \
        mysqli \
        oauth \
        opcache \
        pcntl \
        pdo_mysql \
        pdo_pgsql \
        pgsql \
        pspell \
        redis \
        smbclient \
        soap \
        sockets \
        ssh2 \
        swoole \
        sysvmsg \
        sysvsem \
        sysvshm \
        tidy \
        timezonedb \
        uuid \
        xdebug \
        xhprof \
        xlswriter \
        xsl \
        yaml \
        zip \
        zstd \
    && \
    install-php-extensions @composer && \
    : && \
    rm -rf /tmp/* /var/cache/apk/*

RUN set -ex && \
    ## PDFlib
    ## https://www.pdflib.com/download/pdflib-product-family/
    OS_ARCH=$(uname -m) && \
	case "$OS_ARCH" in \
		amd64 | x86_64) \
			OS_ARCH='x64' \
			;; \
		i?86 | x86) \
			OS_ARCH='x86' \
			;; \
	esac && \
    curl -fSL -k -o pdflib.tar.gz https://www.pdflib.com/binaries/PDFlib/1003/PDFlib-10.0.3-Linux-${OS_ARCH}-php.tar.gz && \
    tar -xzf pdflib.tar.gz && \
    mv PDFlib-* pdflib && \
    PHP_EXT_DIR=$(php-config --extension-dir) && \
    PHP_MAIN_VERSION=$(php --version | grep -Eo '([0-9]{1,}\.)+[0-9]{1,}' | head -n1 | cut -d. -f1-2 | sed 's/\.//g') && \
    cp pdflib/bind/php/php-${PHP_MAIN_VERSION}0-nts/php_pdflib.so $PHP_EXT_DIR && \
    docker-php-ext-enable php_pdflib && \
    rm pdflib.tar.gz && \
    rm -r pdflib && \
    : && \
    PHP_INI_DIR=$(php --ini | grep "Scan for additional .ini files in" | cut -d':' -f2 | cut -d' ' -f2 | head -n1) && \
    ## php runtime environments
    # { \
    # echo "memory_limit = ${PHP_MEMORY_LIMIT}"; \
    # echo "upload_max_filesize = ${MAX_UPLOAD}"; \
    # echo "max_file_uploads = ${PHP_MAX_FILE_UPLOAD}"; \
    # echo "post_max_size = ${PHP_MAX_POST}"; \
    # echo "cgi.fix_pathinfo= 0"; \
    # } > $PHP_INI_DIR/zz-php-env.ini && \
    : && \
    ## timezone
    echo "date.timezone = ${TZ}" > $PHP_INI_DIR/zz-timezone.ini && \
    : && \
    ## recommended opcache settings
    ## https://secure.php.net/manual/en/opcache.installation.php
    { \
    echo 'opcache.memory_consumption=128'; \
    echo 'opcache.interned_strings_buffer=8'; \
    echo 'opcache.max_accelerated_files=4000'; \
    echo 'opcache.revalidate_freq=60'; \
    echo 'opcache.fast_shutdown=1'; \
    echo 'opcache.enable_cli=1'; \
    echo 'opcache.file_cache=/tmp'; \
    } > $PHP_INI_DIR/zz-opcache-recommended.ini && \
    : && \
    ## remove PHP version from the X-Powered-By HTTP header
    ## test: curl -I -H "Accept-Encoding: gzip, deflate" https://www.yourdomain.com
    echo 'expose_php = off' > $PHP_INI_DIR/zz-hide-header-version.ini
