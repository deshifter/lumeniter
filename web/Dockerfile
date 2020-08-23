FROM nginx:alpine

COPY nginx.conf /etc/nginx/

RUN set -xe; \
    apk update && \
    apk upgrade && \
    apk --update add logrotate

RUN apk add --no-cache curl

RUN addgroup -g 82 -S www-data ; \
    adduser -u 82 -D -S -G www-data www-data && exit 0 ; exit 1

# Create 'messages' file used from 'logrotate'
RUN touch /var/log/messages

# Copy 'logrotate' config file
COPY logrotate/web /etc/logrotate.d/

# Set upstream conf and remove the default conf
RUN echo "upstream php-upstream { server php-fpm:9000; }" > /etc/nginx/conf.d/upstream.conf \
    && rm /etc/nginx/conf.d/default.conf

CMD ["nginx"]

EXPOSE 80 443
