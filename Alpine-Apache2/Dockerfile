FROM alpine:latest
## This works!

MAINTAINER Tige Phillips <tige@tigelane.com>

# SYSTEM TOOLS - Alpine
#######################
RUN apk add curl vim git bash

# PYTHON 3 - Alpine
###################
RUN apk add --no-cache --virtual .build-deps g++ python3-dev libffi-dev openssl-dev && \
    apk add --no-cache --update python3 && \
    pip3 install --upgrade pip setuptools && \
    pip3 install pendulum service_identity

# TERRAFORM - Alpine
####################
WORKDIR /tmp
COPY ./alpine-install-terraform.sh ./
RUN chmod 744 alpine-install-terraform.sh && \
    ./alpine-install-terraform.sh && \
    rm ./alpine-install-terraform.sh

# APACHE - Alpine
#################
RUN apk --update add apache2 php5-apache2 && \
    #apk add openrc --no-cache && \
    rm -rf /var/cache/apk/* && \
    sed -i 's/#ServerName www.example.com:80/ServerName localhost/' /etc/apache2/httpd.conf && \
    mkdir -p /run/apache2/

# Upload our files from folder "dist".
COPY dist /var/www/localhost/htdocs

# Manually set up the apache environment variables
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid

# Execute apache2 on run
########################
EXPOSE 80
ENTRYPOINT ["httpd"]
CMD ["-D", "FOREGROUND"]
