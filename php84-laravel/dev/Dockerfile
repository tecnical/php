FROM publiocavalcante/php:8.4

ENV PORT=80
ARG UID=1000
ARG GID=1000

USER root:root
# Installing XDebug
RUN apk add php84-xdebug \
    && apk cache clean \
    && echo \
        $'\n'[xdebug] \
        $'\n'zend_extension=xdebug \
        $'\n'xdebug.mode=debug \
        $'\n'xdebug.discover_client_host=0 \
        $'\n'xdebug.client_host=host.docker.internal \
        $'\n'xdebug.start_with_request=yes \
        >> /etc/php84/php.ini

# Switch to user
USER "${UID}":"${GID}"

CMD ["sh", "-c", "php artisan serve --host=localhost --port ${PORT}"]

