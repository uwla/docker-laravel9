FROM alpine:3.14

RUN apk update
RUN apk --no-cache add php8 php8-fpm php8-opcache php8-mysqli php8-json php8-openssl php8-curl \
    php8-zlib php8-xml php8-phar php8-intl php8-dom php8-xmlreader php8-ctype php8-session \
    php8-mbstring php8-gd php8-tokenizer php8-pdo_mysql php8-pdo php8-bcmath php8-common \
    php8-xmlwriter php8-fileinfo nginx curl

COPY --from=ochinchina/supervisord /usr/local/bin/supervisord /usr/bin/supervisord
COPY --from=composer:1.10 /usr/bin/composer /usr/bin/composer

COPY nginx.conf /etc/nginx/nginx.conf
COPY nginx-default.conf /etc/nginx/conf.d/default.conf
COPY fpm-pool.conf /etc/php8/php-fpm.d/www.conf
COPY php.ini /etc/php8/conf.d/custom.ini
COPY supervisord.conf /etc/supervisor/default.conf

RUN mkdir -p /var/www/ &&\
	rm -rf /var/www/* &&\
	mkdir /var/www/localhost
RUN chown -R nobody:nobody /var/www/ && \
  chown -R nobody:nobody /run && \
  chown -R nobody:nobody /var/lib/nginx && \
  chown -R nobody:nobody /var/log/nginx && \
  chown -R nobody:nobody /etc/nginx
# RUN sed -i 's/;extension=fileinfo/extension=fileinfo/' /etc/php7/php.ini
RUN ln -s /usr/bin/php8 /usr/bin/php

USER nobody
WORKDIR /var/www/localhost

EXPOSE 8080
CMD ["/usr/bin/supervisord", "-c", "/etc/supervisor/default.conf"]
HEALTHCHECK --timeout=4s CMD curl --silent --fail http://127.0.0.1:8080/fpm-ping
