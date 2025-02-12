FROM publiocavalcante/php:8.3

ARG UID=1000
ARG GID=1000

USER root:root
COPY start /usr/local/bin/start
RUN chmod a+X /usr/local/bin/start

# Switch to user
USER "${UID}":"${GID}"

CMD ["/usr/local/bin/start"]
#CMD ["sh"]
