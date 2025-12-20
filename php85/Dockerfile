###############################################################################
# Imagem PHP-8.5 baseada na imagem base alpine instalando o PHP do zero
#
FROM alpine:3.23.2

ARG UID=1000
ARG GID=1000
ARG USER=suporte

# Essentials
RUN apk add --repository https://dl-cdn.alpinelinux.org/alpine/edge/community \
    zip unzip curl git supervisor openssl ca-certificates bash \
    gcc make autoconf g++ zlib-dev \
    && apk cache clean \
    && sed -i 's/bin\/ash/bin\/bash/g' /etc/passwd

#Download the desired package(s)
RUN curl -O https://download.microsoft.com/download/9dcab408-e0d4-4571-a81a-5a0951e3445f/msodbcsql18_18.6.1.1-1_amd64.apk \
    && curl -O https://download.microsoft.com/download/b60bb8b6-d398-4819-9950-2e30cf725fb0/mssql-tools18_18.6.1.1-1_amd64.apk \

    # (Optional) Verify signature, if 'gpg' is missing install it using 'apk add gnupg':
    # curl -O https://download.microsoft.com/download/9dcab408-e0d4-4571-a81a-5a0951e3445f/msodbcsql18_18.6.1.1-1_amd64.sig
    # curl -O https://download.microsoft.com/download/b60bb8b6-d398-4819-9950-2e30cf725fb0/mssql-tools18_18.6.1.1-1_amd64.sig
    # curl https://packages.microsoft.com/keys/microsoft.asc  | gpg --import -
    # gpg --verify msodbcsql18_18.6.1.1-1_amd64.sig msodbcsql18_18.6.1.1-1_amd64.apk
    # gpg --verify mssql-tools18_18.6.1.1-1_amd64.sig mssql-tools18_18.6.1.1-1_amd64.apk \

    #Install the package(s)
    && apk add --allow-untrusted msodbcsql18_18.6.1.1-1_amd64.apk \
    && apk add --allow-untrusted mssql-tools18_18.6.1.1-1_amd64.apk

# Installing PHP
RUN apk add --no-cache php85 php85-fpm php85-ctype php85-curl php85-dom \
    php85-fileinfo php85-ftp php85-iconv php85-json php85-ldap php85-mbstring \
    php85-mysqlnd php85-openssl php85-pdo php85-pdo_sqlite php85-pear \
    php85-phar php85-posix php85-session php85-sodium php85-simplexml \
    php85-sqlite3 php85-tokenizer php85-soap php85-xml php85-xmlreader \
    php85-xmlwriter php85-zlib php85-zip php85-bz2 php85-intl php85-gd \
    php85-imap php85-mysqli php85-bcmath php85-pdo_mysql \
    php85-pdo_sqlite php85-pdo_pgsql php85-exif php85-pcntl ca-certificates \
    php85-dev unixodbc-dev \
    && apk cache clean

RUN ln -s /usr/bin/pecl85 /usr/bin/pecl \
    && ln -s /usr/sbin/php-fpm85 /usr/sbin/php-fpm \
    && ln -s /usr/bin/php85 /usr/bin/php

RUN pecl channel-update pecl.php.net \
    && pecl install sqlsrv \
    && pecl install pdo_sqlsrv \
    && echo "extension=sqlsrv.so" > /etc/php85/conf.d/sqlsrv.ini \
    && echo "extension=pdo_sqlsrv.so" > /etc/php85/conf.d/pdo_sqlsrv.ini \

RUN set -x && \
    (delgroup "${USER}" || true) \
    && addgroup -g "${GID}" -S "${USER}" \
    && adduser -u "${UID}" -D -S -G "${USER}" "${USER}" \
    && mkdir -p /var/www/html && chown "${USER}":"${USER}" /var/www/html

# Clear cache
RUN rm -rf /var/cache/apk/*

COPY php.ini-production /etc/php85/php.ini
COPY zz-custom.conf /etc/php85/php-fpm.d/

# Install Composer
COPY --from=composer:latest /usr/bin/composer /usr/bin/composer

# Configure supervisor
RUN mkdir -p /etc/supervisor.d/
COPY supervisord.ini /etc/supervisor.d/supervisord.ini

# Configurar o Openssl
RUN mkdir -p /usr/local/ssl/certs
RUN /usr/bin/c_rehash

RUN mkdir /etc/ldap \
    && echo "TLS_REQCERT never" >> /etc/openldap/ldap.conf \
    && echo "TLS_REQCERT never" >> /etc/ldap/ldap.conf

# Configure PHP
RUN mkdir -p /run/php/
RUN touch /run/php/php85-fpm.pid

COPY entrypoint.sh /usr/local/bin/

# Switch to user
USER "${UID}":"${GID}"

WORKDIR /var/www/html

STOPSIGNAL SIGQUIT

EXPOSE 9000

#CMD ["supervisord", "-c", "/etc/supervisor.d/supervisord.ini"]
CMD ["bash"]
