FROM ubuntu:latest

MAINTAINER Tige Phillips <tige@tigelane.com>

RUN apt-get update
RUN apt-get -y upgrade

# PYTHON 3 - Ubuntu
###################
# Install Python 3
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install software-properties-common && add-apt-repository ppa:deadsnakes/ppa && apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install python3.6 python-pip
RUN echo "alias python=python3.6" >> ~/.bash_aliases

# SYSTEM TOOLS - Ubuntu
#######################
RUN DEBIAN_FRONTEND=noninteractive pip install --upgrade pip
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install git vim curl unzip wget

# GOLANG - Ubuntu
#################
WORKDIR /root/
ENV GOROOT "/usr/local/go"
ENV GOPATH "/root/go"
ENV PATH "$PATH:$GOROOT/bin:$GOPATH/bin"
RUN mkdir $HOME/go/
#RUN wget https://storage.googleapis.com/golang/go1.8.linux-amd64.tar.gz
RUN wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz
#RUN tar -zxvf go1.8.linux-amd64.tar.gz -C /usr/local/
RUN tar -zxvf go1.10.linux-amd64.tar.gz -C /usr/local/

# TERRAFORM - Ubuntu
####################
# jq JSON pre-processor - support the terraform install
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install jq

COPY ./ubuntu_install_terraform.sh ./
RUN ./ubuntu_install_terraform.sh && rm ./ubuntu_install_terraform.sh

# PACKER - Ubuntu
#################
WORKDIR /root
RUN wget https://releases.hashicorp.com/packer/1.2.4/packer_1.2.4_linux_amd64.zip && unzip packer_1.2.4_linux_amd64.zip

# Packer plugins
RUN mkdir ~/.packer.d && mkdir ~/.packer.d/plugins
RUN wget -O /usr/local/bin/packer-builder-vsphere-iso https://github.com/martezr/packer-builder-vsphere/releases/download/v1.5/packer-builder-vsphere-iso.linux
RUN wget -O /usr/local/bin/packer-builder-vsphere-clone https://github.com/jetbrains-infra/packer-builder-vsphere/releases/download/v2.0/packer-builder-vsphere-clone.linux

RUN mkdir -p $GOPATH/src/github.com/hashicorp
WORKDIR $GOPATH/src/github.com/hashicorp
RUN git clone https://github.com/hashicorp/terraform.git
WORKDIR $GOPATH/src/github.com/hashicorp/terraform

# Packer Cleanup
RUN chmod 755 /usr/local/bin/packer-builder-* && mv packer /usr/local/bin && rm -R packer_*

# ACI GO SDK - Ubuntu
#####################
RUN mkdir -p $GOPATH/src/github.com/ignw
WORKDIR $GOPATH/src/github.com/ignw
RUN git clone https://github.com/IGNW/cisco-aci-go-sdk.git
#RUN git clone --single-branch -b insterbranch https://github.com/IGNW/cisco-aci-go-sdk.git
WORKDIR $GOPATH/src/github.com/ignw/cisco-aci-go-sdk
RUN make fmt && make build

WORKDIR $GOPATH/src/github.com/ignw
# RUN git clone https://github.com/IGNW/terraform-provider-cisco-aci.git
RUN git clone --single-branch -b provider-implementations https://github.com/IGNW/terraform-provider-cisco-aci.git
WORKDIR $GOPATH/src/github.com/ignw/terraform-provider-cisco-aci
RUN make fmt
#RUN make build

# Move all some files around, delete temp files and change permissions as needed
# Golang Cleanup
RUN rm go1.*

# APACHE - Ubuntu
#################
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install apache2 libapache2-mod-php5 php5-mysql php5-gd php-pear php-apc php5-curl curl lynx-cur

# Enable apache mods.
RUN a2enmod php5
RUN a2enmod rewrite
RUN a2enmod cgi

# Update the PHP.ini file, enable <? ?> tags and quieten logging.
RUN sed -i "s/short_open_tag = Off/short_open_tag = On/" /etc/php5/apache2/php.ini
RUN sed -i "s/error_reporting = .*$/error_reporting = E_ERROR | E_WARNING | E_PARSE/" /etc/php5/apache2/php.ini

# Manually set up the apache environment variables
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_PID_FILE /var/run/apache2.pid

# Update the default apache site with the config we created.
ADD site-enabled.conf /etc/apache2/sites-enabled/000-default.conf

# By default when this container runs, simply start apache.
WORKDIR /root
EXPOSE 80
CMD /usr/sbin/apache2ctl -D FOREGROUND
