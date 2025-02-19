###############################################################################
# Imagem PHP-8.4 baseada na imagem base alpine instalando o PHP do zero
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
RUN apk add --no-cache php84-fpm php84-ctype php84-curl php84-dom \
    php84-fileinfo php84-ftp php84-iconv php84-json php84-ldap php84-mbstring \
    php84-mysqlnd php84-openssl php84-pdo php84-pdo_sqlite php84-pear \
    php84-phar php84-posix php84-session php84-sodium php84-simplexml \
    php84-sqlite3 php84-tokenizer php84-soap php84-xml php84-xmlreader \
    php84-xmlwriter php84-zlib php84-zip php84-bz2 php84-intl php84-gd \
    php84-imap php84-mysqli php84-bcmath php84-pdo_mysql php84-opcache \
    php84-pdo_sqlite php84-pdo_pgsql php84-exif php84-pcntl ca-certificates \
    && apk cache clean \
    && ln -s /usr/sbin/php-fpm84 /usr/sbin/php-fpm \
    && ln -s /usr/bin/php84 /usr/bin/php \
    && set -x && \
    (delgroup "${USER}" || true) \
    && addgroup -g "${GID}" -S "${USER}" \
    && adduser -u "${UID}" -D -S -G "${USER}" "${USER}" \
    && mkdir -p /var/www/html && chown "${USER}":"${USER}" /var/www/html

# Clear cache
RUN rm -rf /var/cache/apk/*

COPY php.ini-production /etc/php84/php.ini
COPY zz-custom.conf /etc/php84/php-fpm.d/

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
RUN touch /run/php/php84-fpm.pid

COPY entrypoint.sh /usr/local/bin/

# Switch to user
USER "${UID}":"${GID}"

WORKDIR /var/www/html

STOPSIGNAL SIGQUIT

EXPOSE 9000

CMD ["supervisord", "-c", "/etc/supervisor.d/supervisord.ini"]
#CMD ["bash"]
