FROM httpd:2.4.52-alpine3.15

RUN apk update; \
    apk upgrade;

ARG SERVICE_DIR="./apache"
COPY ./.shared/scripts/ /tmp/scripts/
RUN chmod -R 777 /tmp/scripts/

# set timezone
ARG TZ=UTC
RUN /tmp/scripts/set_timezone.sh ${TZ}

# add users
ARG APP_USER=www-data
ARG APP_GROUP=www-data
ARG APP_USER_ID=1000
ARG APP_GROUP_ID=1000

RUN /tmp/scripts/create_user.sh ${APP_USER} ${APP_GROUP} ${APP_USER_ID} ${APP_GROUP_ID}

RUN apk --no-cache add curl
RUN apk --no-cache add gdb
RUN apk --no-cache add git
RUN apk --no-cache add htop
RUN apk --no-cache add iproute2
RUN apk --no-cache add ltrace
RUN apk --no-cache add make
RUN apk --no-cache add procps
RUN apk --no-cache add strace
RUN apk --no-cache add sudo
RUN apk --no-cache add sysstat
RUN apk --no-cache add unzip
RUN apk --no-cache add vim
RUN apk --no-cache add wget
RUN apk --no-cache add bind-tools

# apache app config
COPY ${SERVICE_DIR}/sites-available/* /usr/local/apache2/conf/
ARG APP_CODE_PATH
ARG APP_HOST
ARG COMPOSE_PROJECT_NAME
RUN /tmp/scripts/modify_config.sh /usr/local/apache2/conf/httpd.conf \
    "__APACHE_ROOT" \
    "${APP_CODE_PATH}/public" \
    ;
RUN /tmp/scripts/modify_config.sh /usr/local/apache2/conf/httpd.conf \
    "__APP_HOST" \
    "${APP_HOST}" \
    ;
RUN /tmp/scripts/modify_config.sh /usr/local/apache2/conf/httpd.conf \
    "__COMPOSE_PROJECT_NAME" \
    "${COMPOSE_PROJECT_NAME}" \
    ;

# workdir
WORKDIR /usr/local/apache2/

# entrypoint
RUN mkdir -p /bin/docker-entrypoint/ \
    && cp /tmp/scripts/docker-entrypoint/* /bin/docker-entrypoint/ \
    && chmod +x -R /bin/docker-entrypoint/ \
    ;

# cleanup
RUN /tmp/scripts/cleanup.sh
ENTRYPOINT ["/bin/docker-entrypoint/resolve-docker-host-ip.sh"]
CMD ["httpd-foreground"]
EXPOSE 80 443
