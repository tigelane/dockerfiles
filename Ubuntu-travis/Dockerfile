FROM ubuntu:latest

MAINTAINER Tige Phillips <tige@tigelane.com>

RUN apt-get update
RUN apt-get -y upgrade

# Install some normal Tige stuff - Ubuntu
#########################################
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install git vim curl unzip wget software-properties-common

RUN apt-get -y install gcc libffi-dev make
RUN apt-get -y install ruby-full
RUN gem install travis


RUN curl https://raw.githubusercontent.com/dlenski/travis-encrypt-sh/HEAD/travis-encrypt > ~/bin/travis-encrypt
RUN chmod +x !$
RUN travis-encrypt
