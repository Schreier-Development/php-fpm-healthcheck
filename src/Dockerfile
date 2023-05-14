FROM php:fpm-alpine

RUN apk add --no-cache fcgi
RUN set -xe && echo "pm.status_path = /status" >> /usr/local/etc/php-fpm.d/zz-docker.conf
COPY ./src/php-fpm-healthcheck /usr/local/bin/
RUN chmod +x /usr/local/bin/php-fpm-healthcheck
HEALTHCHECK --interval=5s --timeout=5s --retries=15 \
    CMD php-fpm-healthcheck || exit 1

WORKDIR /codetem