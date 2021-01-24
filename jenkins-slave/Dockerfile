FROM ubuntu:20.04

# install packages
RUN apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y openssh-server vim gnupg git openjdk-8-jdk
RUN apt-get clean

# create jenkins user
RUN groupadd -g 1000 jenkins
RUN useradd -d /home/jenkins -u 1000 -g 1000 -m -s /bin/bash jenkins

# set working directory
WORKDIR /home/jenkins

# continue as jenkins
USER jenkins

# configure GPG
RUN mkdir .gnupg
RUN chmod 0700 -R .gnupg
RUN echo allow-loopback-pinentry > .gnupg/gpg-agent.conf
RUN echo "use-agent\npinentry-mode loopback" > .gnupg/gpg.conf
COPY secrets/jenkins-gmail.asc secrets/passphrase.txt /home/jenkins/
RUN gpg --no-tty --passphrase-file passphrase.txt --import jenkins-gmail.asc
RUN rm jenkins-gmail.asc passphrase.txt

# configure Git
RUN git config --global user.email "jenkins.ci.build@gmail.com"
RUN git config --global user.name "Jenkins"

# configure Maven
RUN mkdir .m2
COPY secrets/settings.xml .m2/settings.xml

# configure SSH
RUN mkdir .ssh
COPY secrets/jenkins-master.pub .ssh/authorized_keys
RUN ssh-keyscan github.com >> .ssh/known_hosts

# continue as root
USER root

# start SSH
RUN mkdir /var/run/sshd
CMD exec /usr/sbin/sshd -De

# ports
EXPOSE 22