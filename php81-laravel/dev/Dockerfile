FROM publiocavalcante/php:8.1

ENV PORT=80
ARG UID=1000
ARG GID=1000

USER root:root
# Installing XDebug
RUN apk add php81-xdebug && \
    echo \
        $'\n'[xdebug] \
        $'\n'zend_extension=xdebug \
        $'\n'xdebug.mode=debug \
        $'\n'xdebug.discover_client_host=0 \
        $'\n'xdebug.client_host=host.docker.internal \
        $'\n'xdebug.start_with_request=yes \
        >> /etc/php81/php.ini

# Switch to user
USER "${UID}":"${GID}"

CMD ["sh", "-c", "php artisan serve --host=0.0.0.0 --port ${PORT}"]
