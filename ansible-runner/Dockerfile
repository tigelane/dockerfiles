FROM python:3.10-slim-buster

#
# System upgrades or tools
##########################
RUN apt-get update
RUN pip install --upgrade pip

#
# Install ansible application requirements
##########################################
RUN apt-get install -y sshpass
RUN pip install ansible
RUN pip install paramiko

#
# Accept the ssh keys for our devices 
#####################################
WORKDIR /root
RUN mkdir ~/.ssh
RUN echo "Host *" > /root/.ssh/config && echo " StrictHostKeyChecking no" >> /root/.ssh/config
