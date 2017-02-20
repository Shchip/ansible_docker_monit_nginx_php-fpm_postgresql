FROM ubuntu:latest

RUN \
# Install Web
    apt-get update && \
    apt-get install -y nginx php-fpm php-pgsql php-cgi phppgadmin && \

# Add link phppgadmin to web directory
    ln -s /usr/share/phppgadmin /var/www


# Disable ipv6
ADD ipv6off.sh /etc/rc.local

# Add nginx config
ADD default /etc/nginx/sites-available/default

# Copy all files to site directory
COPY ./site /var/www/site

# Add phppgadmin config
ADD config.inc.php /var/www/phppgadmin/conf/config.inc.php

EXPOSE 80

CMD service php7.0-fpm start && nginx -g "daemon off;"
