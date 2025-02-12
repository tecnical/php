FROM publiocavalcante/php:8.1

ARG UID=1000
ARG GID=1000

USER root:root
COPY start /usr/local/bin/start
RUN chmod a+x /usr/local/bin/start

# Switch to user
USER "${UID}":"${GID}"
#USER suporte:suporte

# Building process
#COPY html/ /var/www/html/
#RUN composer install # --no-dev
#RUN chown -R www-data:www-data /var/www/html/
#RUN chmod -R 777 /var/www/html/storage/logs
#RUN chmod -R 777 /var/www/html/storage/framework

CMD ["/usr/local/bin/start"]

