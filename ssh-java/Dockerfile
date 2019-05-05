FROM centos:latest

##########################################################################
### update glibc-common for locale files
RUN yum update -y glibc-common

##########################################################################
# all yum installations here
RUN yum install -y sudo passwd openssh-server openssh-clients tar screen crontabs strace telnet perl libpcap bc patch ntp dnsmasq unzip pax which

##########################################################################
# add epel repository
RUN rpm -Uvh https://mirrors.tuna.tsinghua.edu.cn/epel/epel-release-latest-7.noarch.rpm

RUN (yum install -y hiera lsyncd sshpass rng-tools java-1.8.0-openjdk)

# start sshd to generate host keys, patch sshd_config and enable yum repos
RUN (ssh-keygen -t rsa -f /etc/ssh/ssh_host_rsa_key -N ""; \
     ssh-keygen -t ecdsa -f /etc/ssh/ssh_host_ecdsa_key -N ""; \
     ssh-keygen -t ed25519 -f /etc/ssh/ssh_host_ed25519_key -N ""; \
     sed -i 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config; \
     sed -i 's/#UsePAM no/UsePAM no/g' /etc/ssh/sshd_config; \
     sed -i 's/#UseDNS yes/UseDNS no/g' /etc/ssh/sshd_config; \
     sed -i 's/GSSAPIAuthentication yes/GSSAPIAuthentication no/g' /etc/ssh/sshd_config; \
     sed -i 's/#PermitRootLogin yes/PermitRootLogin yes/' /etc/ssh/sshd_config; \
     sed -i 's/enabled=0/enabled=1/' /etc/yum.repos.d/CentOS-Base.repo)

# ssh without key
RUN ssh-keygen -t rsa -f ~/.ssh/id_rsa -P '' && \
    cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

RUN (rm -f /var/lib/rpm/.rpm.lock; \
     echo "StrictHostKeyChecking=no" > /root/.ssh/config; \
     echo "UserKnownHostsFile=/dev/null" >> /root/.ssh/config)

# add a simple script that can auto-detect the appropriate JAVA_HOME value
# based on whether the JDK or only the JRE is installed
RUN { \
		echo '#!/bin/sh'; \
		echo 'set -e'; \
		echo; \
		echo 'dirname "$(dirname "$(readlink -f "$(which javac || which java)")")"'; \
	} > /usr/local/bin/docker-java-home \
	&& chmod +x /usr/local/bin/docker-java-home
ENV JAVA_HOME /usr/lib/jvm/jre
ENV PATH $PATH:/usr/lib/jvm/jre/bin:/usr/lib/jvm/jre-openjdk/bin

##########################################################################
# passwords 
RUN echo "root:root" | chpasswd

EXPOSE 22
CMD /usr/sbin/sshd -D