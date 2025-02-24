###############################################################################
# Imagem PHP-8.3 baseada na imagem base alpine instalando o PHP do zero
#
FROM alpine:3.21.2

ARG UID=1000
ARG GID=1000
ARG USER=suporte

# Essentials
RUN apk add --repository http://dl-cdn.alpinelinux.org/alpine/edge/community \
    zip unzip curl git supervisor openssl ca-certificates bash \
    && apk cache clean \
    && sed -i 's/bin\/ash/bin\/bash/g' /etc/passwd

# Installing PHP
RUN apk add --no-cache php83-fpm php83-ctype php83-curl php83-dom \
    php83-fileinfo php83-ftp php83-iconv php83-json php83-ldap php83-mbstring \
    php83-mysqlnd php83-openssl php83-pdo php83-pdo_sqlite php83-pear \
    php83-phar php83-posix php83-session php83-sodium php83-simplexml \
    php83-sqlite3 php83-tokenizer php83-soap php83-xml php83-xmlreader \
    php83-xmlwriter php83-zlib php83-zip php83-bz2 php83-intl php83-gd \
    php83-imap php83-mysqli php83-bcmath php83-pdo_mysql php83-opcache \
    php83-pdo_sqlite php83-pdo_pgsql php83-exif php83-pcntl ca-certificates \
    && apk cache clean \
    && ln -s /usr/sbin/php-fpm83 /usr/sbin/php-fpm \
    && ln -s /usr/bin/php83 /usr/bin/php \
    && set -x && \
    (delgroup "${USER}" || true) \
    && addgroup -g "${GID}" -S "${USER}" \
    && adduser -u "${UID}" -D -S -G "${USER}" "${USER}" \
    && mkdir -p /var/www/html && chown "${USER}":"${USER}" /var/www/html

    
# Clear cache
RUN rm -rf /var/cache/apk/*

COPY php.ini-production /etc/php83/php.ini
COPY zz-custom.conf /etc/php83/php-fpm.d/

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
RUN touch /run/php/php83-fpm.pid

COPY entrypoint.sh /usr/local/bin/

# Switch to user
USER "${UID}":"${GID}"

WORKDIR /var/www/html

STOPSIGNAL SIGQUIT

EXPOSE 9000

CMD ["supervisord", "-c", "/etc/supervisor.d/supervisord.ini"]
#CMD ["bash"]
