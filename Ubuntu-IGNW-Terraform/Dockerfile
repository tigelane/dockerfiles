FROM ubuntu:latest

MAINTAINER Tige Phillips <tige@tigelane.com>

RUN apt-get update
RUN apt-get -y upgrade

# Install some normal Tige stuff - Ubuntu
#########################################
RUN DEBIAN_FRONTEND=noninteractive apt-get -y install git vim curl unzip wget software-properties-common

# Install Terraform - Ubuntu
############################
WORKDIR /root/
COPY ./ubuntu_install_terraform.sh ./
RUN chmod 744 ubuntu_install_terraform.sh && \
    ./ubuntu_install_terraform.sh && \
    rm ./ubuntu_install_terraform.sh

# Install GO - Ubuntu
#####################
ENV GOPATH "/root/go"
ENV PATH "$PATH:/usr/local/go/bin:$GOPATH/bin"
RUN mkdir /root/go/
WORKDIR /tmp
RUN wget https://dl.google.com/go/go1.10.linux-amd64.tar.gz && \
    tar -C /usr/local/ -zxvf go1.10.linux-amd64.tar.gz

# IGNW Terraform install
########################
RUN mkdir -p $GOPATH/src/github.com/terraform-providers
WORKDIR $GOPATH/src/github.com/ignw
RUN git clone --single-branch -b provider-implementations https://github.com/IGNW/terraform-provider-cisco-aci.git
WORKDIR $GOPATH/src/github.com/ignw/terraform-provider-cisco-aci

#RUN gofmt
#RUN go get
#RUN make build
