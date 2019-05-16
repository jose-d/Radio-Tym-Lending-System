FROM centos:7
ENV container docker

# prepare systemd 
RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == \
systemd-tmpfiles-setup.service ] || rm -f $i; done); \
rm -f /lib/systemd/system/multi-user.target.wants/*;\
rm -f /etc/systemd/system/*.wants/*;\
rm -f /lib/systemd/system/local-fs.target.wants/*; \
rm -f /lib/systemd/system/sockets.target.wants/*udev*; \
rm -f /lib/systemd/system/sockets.target.wants/*initctl*; \
rm -f /lib/systemd/system/basic.target.wants/*;\
rm -f /lib/systemd/system/anaconda.target.wants/*;

# update sw

RUN yum -y update

# install epel and nice tools

RUN yum -y install yum-utils epel-release yum-plugin-versionlock curl git vim zip unzip && \
    yum -y install nginx supervisor

# install php

RUN yum -y install http://rpms.remirepo.net/enterprise/remi-release-7.rpm && \
    yum-config-manager --enable remi-php71 && \
    yum -y --enablerepo=remi,remi-php71 install php-fpm php-common && \
    yum -y --enablerepo=remi,remi-php71 install php-opcache php-pecl-apcu php-cli php-pear php-pdo php-mysqlnd php-pgsql php-pecl-mongodb php-pecl-redis php-pecl-memcache php-pecl-memcached php-gd php-mbstring php-mcrypt php-xml


# install composer

RUN cd / && \
    mkdir /composer && \
    cd /composer && \
    php -r "copy('https://getcomposer.org/installer', 'composer-setup.php');" && \
    php -r "if (hash_file('sha384', 'composer-setup.php') === '48e3236262b34d30969dca3c37281b3b4bbe3221bda826ac6a9a62d6444cdb0dcd0615698a5cbe587c3f0fe57a54d8f5') { echo 'Installer verified'; } else { echo 'Installer corrupt'; unlink('composer-setup.php'); } echo PHP_EOL;" && \
    php composer-setup.php && \
    php -r "unlink('composer-setup.php');"

# get app

RUN cd / && \
    mkdir app && \
    cd /app && \
    git clone https://github.com/skaut/Radio-Tym-Lending-System.git .

# update composer

RUN cd /app && \
    /composer/composer.phar update


COPY ./config/nginx.conf /etc/nginx/nginx.conf
COPY ./config/php-fpm.conf /etc/php-fpm.conf
COPY ./config/supervisord.conf /etc/supervisord.conf
COPY ./config/www.conf /etc/php-fpm.d/www.conf


VOLUME [ "/sys/fs/cgroup" ]

#RUN ln -sf /dev/stdout /var/log/nginx/access.log \
#	&& ln -sf /dev/stderr /var/log/nginx/error.log

EXPOSE 80

STOPSIGNAL SIGTERM

#CMD ["nginx", "-g", "daemon off;"]
CMD ["/usr/bin/supervisord","-c","/etc/supervisord.conf"]
