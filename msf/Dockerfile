FROM centos:centos7

# -----------------------------------------------------------------------------
# Make src dir
# -----------------------------------------------------------------------------
ENV HOME /home/worker
ENV SRC_DIR $HOME/src
RUN /usr/bin/mkdir -p ${SRC_DIR}
#ADD src ${SRC_DIR}
ADD pkg ${SRC_DIR}

#RUN export HTTP_PROXY=http://192.168.31.16:7890 \
#    && export HTTPS_PROXY=http://192.168.31.16:7890\
#    && export FTP_PROXY=http://192.168.31.16:7890

# -----------------------------------------------------------------------------
# Install Development tools
# -----------------------------------------------------------------------------
RUN rpm --import /etc/pki/rpm-gpg/RPM* \
#    && curl --silent --location https://raw.githubusercontent.com/nodesource/distributions/master/rpm/setup_6.x | bash - \
    && yum -y update \
    && yum groupinstall -y "Development tools" \
    && yum install -y gcc-c++ zlib-devel bzip2-devel openssl \
    openssl-devel ncurses-devel sqlite-devel wget \
    && rm -rf /var/cache/{yum,ldconfig}/* \
    && rm -rf /etc/ld.so.cache \
    && yum clean all

# -----------------------------------------------------------------------------
# Install curl
# -----------------------------------------------------------------------------
ENV CURL_INSTALL_DIR ${HOME}/libcurl
RUN cd ${SRC_DIR} \
#    && wget -O curl-7.55.1.tar.gz http://curl.askapache.com/download/curl-7.55.1.tar.gz \
    && tar xzf curl-7.55.1.tar.gz \
    && cd curl-7.55.1 \
    && ./configure --prefix=${CURL_INSTALL_DIR} \
    && make 1>/dev/null \
    && make install \
    && rm -rf ${SRC_DIR}/curl*

# -----------------------------------------------------------------------------
# Set Time Zone
# -----------------------------------------------------------------------------
RUN ln -sf /usr/share/zoneinfo/Asia/Chongqing /etc/localtime \
#        && sed -i \
#            -e 's/^UsePAM yes/#UsePAM yes/g' \
#            -e 's/^#UsePAM no/UsePAM no/g' \
#            -e 's/#UsePrivilegeSeparation.*/UsePrivilegeSeparation no/g' \
#            -e 's/^#UseDNS yes/UseDNS no/g' \
#            /etc/ssh/sshd_config \
        && echo "root" | passwd --stdin root \
        && ssh-keygen -q -b 1024 -N '' -t rsa -f /etc/ssh/ssh_host_rsa_key \
        && ssh-keygen -q -b 1024 -N '' -t dsa -f /etc/ssh/ssh_host_dsa_key \
        && echo "NETWORKING=yes" > /etc/sysconfig/network


# -----------------------------------------------------------------------------
# Install ImageMagick
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O ImageMagick.tar.gz https://www.imagemagick.org/download/ImageMagick.tar.gz \
    && tar zxf ImageMagick.tar.gz \
    && rm -rf ImageMagick.tar.gz \
    && ImageMagickPath=`ls` \
    && cd ${ImageMagickPath} \
    && ./configure \
    && make \
    && make install \
    && rm -rf $SRC_DIR/ImageMagick*

# -----------------------------------------------------------------------------
# Install hiredis
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O hiredis-0.13.3.tar.gz https://github.com/redis/hiredis/archive/v0.13.3.tar.gz \
    && tar zxvf hiredis-0.13.3.tar.gz \
    && cd hiredis-0.13.3 \
    && make \
    && make install \
    && echo "/usr/local/lib" > /etc/ld.so.conf.d/local.conf \
    && ldconfig \
    && rm -rf $SRC_DIR/hiredis-*

# -----------------------------------------------------------------------------
# Install libmemcached using by php-memcached
# -----------------------------------------------------------------------------
ENV LIB_MEMCACHED_INSTALL_DIR /usr/local/
RUN cd ${SRC_DIR} \
#    && wget -O libmemcached-1.0.18.tar.gz https://launchpad.net/libmemcached/1.0/1.0.18/+download/libmemcached-1.0.18.tar.gz \
    && tar xzf libmemcached-1.0.18.tar.gz \
    && cd libmemcached-1.0.18 \
    && ./configure --prefix=$LIB_MEMCACHED_INSTALL_DIR --with-memcached 1>/dev/null \
    && make 1>/dev/null \
    && make install \
    && rm -rf ${SRC_DIR}/libmemcached*

# -----------------------------------------------------------------------------
# Install libmcrypt using by php-mcrypt
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O libmcrypt-2.5.8.tar.gz https://nchc.dl.sourceforge.net/project/mcrypt/Libmcrypt/2.5.8/libmcrypt-2.5.8.tar.gz \
    && tar xzf libmcrypt-2.5.8.tar.gz \
    && cd libmcrypt-2.5.8 \
    && ./configure 1>/dev/null \
    && make 1>/dev/null \
    && make install \
    && echo "/usr/local/lib64" >> /etc/ld.so.conf.d/local.conf \
    && echo "/usr/local/src/libmcrypt-2.5.8/lib/.libs" >> /etc/ld.so.conf.d/local.conf \
    && chmod gu+x /etc/ld.so.conf.d/local.conf \
    && ldconfig -v

# -----------------------------------------------------------------------------
# Install re2c for PHP
# -----------------------------------------------------------------------------
run cd $SRC_DIR \
#    && wget -O re2c-1.0.tar.gz https://excellmedia.dl.sourceforge.net/project/re2c/old/re2c-1.0.tar.gz \
    && tar xzf re2c-1.0.tar.gz \
    && cd re2c-1.0 \
    && ./configure \
    && make \
    && make install \
    && rm -rf ${SRC_DIR}/re2c*

#RUN export http_proxy=http://127.0.0.1:58591
# -----------------------------------------------------------------------------
# Install PHP
# -----------------------------------------------------------------------------
ENV phpversion 7.1.9
ENV PHP_INSTALL_DIR ${HOME}/php
RUN cd ${SRC_DIR} \
    && yum install -y freetype-devel libevent libevent-devel libxml2 libxml2-devel ncurses ncurses-devel  openssl openssl-devel  libjpeg libjpeg-devel libpng libpng-devel zlib-devel  bzip2 bzip2-devel libmcrypt libmcrypt-devel openldap openldap-devel unixODBC unixODBC-devel libtool-ltdl libtool-ltdl-devel net-snmp-devel expat-devel libxslt-devel\
    && ls -l \
#    && wget -O php-${phpversion}.tar.gz http://cn2.php.net/distributions/php-${phpversion}.tar.gz \
#    && wget -O php-${phpversion}.tar.gz http://cn2.php.net/distributions/php-${phpversion}.tar.gz \
    && tar xzf php-${phpversion}.tar.gz \
    && cd php-${phpversion} \
    && ./configure \
       --prefix=${PHP_INSTALL_DIR} \
       --with-config-file-path=${PHP_INSTALL_DIR}/etc \
       --with-config-file-scan-dir=${PHP_INSTALL_DIR}/etc/php.d \
       --sysconfdir=${PHP_INSTALL_DIR}/etc \
       --with-libdir=lib64 \
       --enable-mysqlnd \
       --enable-zip \
       --enable-exif \
       --enable-ftp \
       --enable-mbstring \
       --enable-mbregex \
       --enable-fpm \
       --enable-bcmath \
       --enable-pcntl \
       --enable-soap \
       --enable-sockets \
       --enable-shmop \
       --enable-sysvmsg \
       --enable-sysvsem \
       --enable-sysvshm \
       --enable-gd-native-ttf \
       --enable-wddx \
       --enable-opcache \
       --with-gettext \
       --with-xsl \
       --with-libexpat-dir \
       --with-xmlrpc \
       --with-snmp \
       --with-ldap \
       --enable-mysqlnd \
       --with-mysqli=mysqlnd \
       --with-pdo-mysql=mysqlnd \
       --with-pdo-odbc=unixODBC,/usr \
       --with-gd \
       --with-jpeg-dir \
       --with-png-dir \
       --with-zlib-dir \
       --with-freetype-dir \
       --with-zlib \
       --with-bz2 \
       --with-openssl \
       --with-curl=${CURL_INSTALL_DIR} \
       --with-mcrypt \
       --with-mhash \
    && make 1>/dev/null \
    && make install \
    && rm -rf ${PHP_INSTALL_DIR}/lib/php.ini \
    && cp -f php.ini-development ${PHP_INSTALL_DIR}/lib/php.ini \
    && rm -rf ${SRC_DIR}/php* ${SRC_DIR}/libmcrypt*

# -----------------------------------------------------------------------------
# Install yaml and PHP yaml extension
# -----------------------------------------------------------------------------
RUN cd $SRC_DIR \
#    && wget -O yaml-0.1.7.tar.gz http://pyyaml.org/download/libyaml/yaml-0.1.7.tar.gz \
    && tar xzf yaml-0.1.7.tar.gz \
    && cd yaml-0.1.7 \
    && ./configure --prefix=/usr/local \
    && make >/dev/null \
    && make install \
    && cd $SRC_DIR \
    && wget -O yaml-2.0.2.tgz https://pecl.php.net/get/yaml-2.0.2.tgz \
    && tar xzf yaml-2.0.2.tgz \
    && cd yaml-2.0.2 \
    && $PHP_INSTALL_DIR/bin/phpize \
    && ./configure --with-yaml=/usr/local --with-php-config=$PHP_INSTALL_DIR/bin/php-config \
    && make >/dev/null \
    && make install \
    && rm -rf $SRC_DIR/yaml-*

# -----------------------------------------------------------------------------
# Install PHP mongodb extensions
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O mongodb-1.3.2.tgz https://pecl.php.net/get/mongodb-1.3.2.tgz \
    && tar zxf mongodb-1.3.2.tgz \
    && cd mongodb-1.3.2 \
    && ${PHP_INSTALL_DIR}/bin/phpize \
    && ./configure --with-php-config=$PHP_INSTALL_DIR/bin/php-config 1>/dev/null \
    && make clean \
    && make \
    && make install \
    && rm -rf ${SRC_DIR}/mongodb-*
# -----------------------------------------------------------------------------
# Install amqp
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O rabbitmq-c-0.8.0.tar.gz  https://github.com/alanxz/rabbitmq-c/archive/v0.8.0.tar.gz \
    && tar zxf rabbitmq-c-0.8.0.tar.gz \
    && cd rabbitmq-c-0.8.0 \
    && autoreconf -i \
    && ./configure --prefix=${HOME}/rabbitmq-c 1>/dev/null \
    && make 1>/dev/null \
    && make install \
    && rm -rf ${SRC_DIR}/rabbitmq-c*
# -----------------------------------------------------------------------------
# Install PHP amqp extensions
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O amqp-1.9.3.tgz http://pecl.php.net/get/amqp-1.9.3.tgz \
    && tar zxvf amqp-1.9.3.tgz \
    && cd amqp-1.9.3 \
    && ${PHP_INSTALL_DIR}/bin/phpize \
    && ./configure --with-php-config=$PHP_INSTALL_DIR/bin/php-config --with-librabbitmq-dir=${HOME}/rabbitmq-c 1>/dev/null \
    && make clean \
    && make 1>/dev/null \
    && make install \
    && rm -rf ${SRC_DIR}/amqp*

# -----------------------------------------------------------------------------
# Install PHP redis extensions
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O redis-3.1.3.tgz https://pecl.php.net/get/redis-3.1.3.tgz \
    && tar zxf redis-3.1.3.tgz \
    && cd redis-3.1.3 \
    && ${PHP_INSTALL_DIR}/bin/phpize \
    && ./configure --with-php-config=$PHP_INSTALL_DIR/bin/php-config 1>/dev/null \
    && make clean \
    && make 1>/dev/null \
    && make install \
    && rm -rf ${SRC_DIR}/redis-*

# -----------------------------------------------------------------------------
# Install PHP imagick extensions
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O imagick-3.4.3.tgz https://pecl.php.net/get/imagick-3.4.3.tgz \
    && tar zxf imagick-3.4.3.tgz \
    && cd imagick-3.4.3 \
    && ${PHP_INSTALL_DIR}/bin/phpize \
    && ./configure --with-php-config=$PHP_INSTALL_DIR/bin/php-config \
    --with-imagick 1>/dev/null \
    && make clean \
    && make 1>/dev/null \
    && make install \
    && rm -rf ${SRC_DIR}/imagick-*

# -----------------------------------------------------------------------------
# Install PHP igbinary extensions
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O igbinary-2.0.1.tgz https://pecl.php.net/get/igbinary-2.0.1.tgz \
    && tar zxf igbinary-2.0.1.tgz \
    && cd igbinary-2.0.1 \
    && ${PHP_INSTALL_DIR}/bin/phpize \
    && ./configure --with-php-config=$PHP_INSTALL_DIR/bin/php-config 1>/dev/null \
    && make clean \
    && make 1>/dev/null \
    && make install \
    && rm -rf ${SRC_DIR}/igbinary-*

# -----------------------------------------------------------------------------
# Install PHP memcached extensions
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O memcached-3.0.3.tgz https://pecl.php.net/get/memcached-3.0.3.tgz \
    && tar xzf memcached-3.0.3.tgz \
    && cd memcached-3.0.3 \
    && ${PHP_INSTALL_DIR}/bin/phpize \
    && ./configure --enable-memcached --with-php-config=$PHP_INSTALL_DIR/bin/php-config \
       --with-libmemcached-dir=$LIB_MEMCACHED_INSTALL_DIR --disable-memcached-sasl 1>/dev/null \
    && make 1>/dev/null \
    && make install \
    && rm -rf ${SRC_DIR}/memcached-*

# -----------------------------------------------------------------------------
# Install PHP yac extensions
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O yac-2.0.2.tgz https://pecl.php.net/get/yac-2.0.2.tgz \
    && tar zxf yac-2.0.2.tgz\
    && cd yac-2.0.2 \
    && ${PHP_INSTALL_DIR}/bin/phpize \
    && ./configure --with-php-config=$PHP_INSTALL_DIR/bin/php-config \
    && make 1>/dev/null \
    && make install \


    && rm -rf $SRC_DIR/yac-*

# -----------------------------------------------------------------------------
# Install PHP swoole extensions
# -----------------------------------------------------------------------------
ENV swooleVersion 1.9.22
RUN cd ${SRC_DIR} \
#    && wget -O swoole-${swooleVersion}.tar.gz https://github.com/swoole/swoole-src/archive/v${swooleVersion}.tar.gz \
    && tar zxf swoole-${swooleVersion}.tar.gz \
    && cd swoole-src-${swooleVersion}/ \
    && ${PHP_INSTALL_DIR}/bin/phpize \
    && ./configure --with-php-config=$PHP_INSTALL_DIR/bin/php-config --enable-async-redis --enable-openssl \
    && make clean 1>/dev/null \
    && make 1>/dev/null \
    && make install \
    && rm -rf ${SRC_DIR}/swoole*

# -----------------------------------------------------------------------------
# Install PHP inotify extensions
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O inotify-2.0.0.tgz https://pecl.php.net/get/inotify-2.0.0.tgz \
    && tar zxf inotify-2.0.0.tgz \
    && cd inotify-2.0.0 \
    && ${PHP_INSTALL_DIR}/bin/phpize \
    && ./configure --with-php-config=$PHP_INSTALL_DIR/bin/php-config 1>/dev/null \
    && make clean \
    && make 1>/dev/null \
    && make install \
    && rm -rf ${SRC_DIR}/inotify-*

# -----------------------------------------------------------------------------
# Install phpunit
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O phpunit.phar https://phar.phpunit.de/phpunit.phar \
    && mv phpunit.phar ${PHP_INSTALL_DIR}/bin/phpunit \
    && chmod +x ${PHP_INSTALL_DIR}/bin/phpunit

# -----------------------------------------------------------------------------
# Install php composer
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && curl -sS https://getcomposer.org/installer | $PHP_INSTALL_DIR/bin/php \
    && $PHP_INSTALL_DIR/bin/php installer \
    && chmod +x composer.phar \
    && mv composer.phar ${PHP_INSTALL_DIR}/bin/composer

# -----------------------------------------------------------------------------
# Install PhpDocumentor
# -----------------------------------------------------------------------------
RUN $PHP_INSTALL_DIR/bin/pear install -a PhpDocumentor

RUN cd ${PHP_INSTALL_DIR} \
    && bin/php bin/composer self-update \
    && bin/pear install PHP_CodeSniffer-2.3.4 \
    && rm -rf /tmp/*

# -----------------------------------------------------------------------------
# Install jq
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
#    && wget -O jq-1.5.tar.gz https://github.com/stedolan/jq/archive/jq-1.5.tar.gz \
    && tar zxf jq-1.5.tar.gz \
    && cd jq-jq-1.5 \
    && ./configure --disable-maintainer-mode \
    && make \
    && make install \
    && rm -rf ${SRC_DIR}/jq-*

# -----------------------------------------------------------------------------
# Install Apache ab
# -----------------------------------------------------------------------------
#RUN cd ${HOME} \
#    && yum -y remove httpd \
#    && mkdir httpd \
#    && cd httpd \
#    && yumdownloader httpd-tools \
#    && rpm2cpio httpd-tools* | cpio -idmv \
#    && mkdir -p /home/worker/bin \
#    && mv ./usr/bin/ab /home/worker/bin \
#    && cd ${HOME} && rm -rf /home/worker/httpd

# -----------------------------------------------------------------------------
# Update Git
# -----------------------------------------------------------------------------
RUN cd ${SRC_DIR} \
    && yum -y remove git subversion \
#    && wget -O git-2.14.1.tar.gz https://github.com/git/git/archive/v2.14.1.tar.gz \
    && tar zxf git-2.14.1.tar.gz \
    && cd git-2.14.1 \
    && make configure \
    && ./configure --without-iconv --prefix=/usr/local/ --with-curl=${CURL_INSTALL_DIR} \
    && make \
    && make install \
    && rm -rf $SRC_DIR/git-2*


# -----------------------------------------------------------------------------
# Add user worker
# -----------------------------------------------------------------------------
#RUN useradd -M -u 1000 worker \
#    && echo "worker" | passwd --stdin worker \
#    && echo 'worker  ALL=(ALL)  NOPASSWD: ALL' > /etc/sudoers.d/worker \
#    && sed -i \
#        -e 's/^#PermitRootLogin yes/PermitRootLogin no/g' \
#        -e 's/^PermitRootLogin yes/PermitRootLogin no/g' \
#        -e 's/^#PermitUserEnvironment no/PermitUserEnvironment yes/g' \
#        -e 's/^PermitUserEnvironment no/PermitUserEnvironment yes/g' \
#        /etc/ssh/sshd_config \
#    && chmod a+x /run.sh \
#    && chmod a+x ${PHP_INSTALL_DIR}/bin/checkstyle \
#    && chmod a+x ${PHP_INSTALL_DIR}/bin/mergeCoverReport

# -----------------------------------------------------------------------------
# clean tmp file
# -----------------------------------------------------------------------------
RUN rm -rf ${SRC_DIR}/*
RUN rm -rf /tmp/*

#ENTRYPOINT ["/run.sh"]

EXPOSE 22 80 443
WORKDIR /home/worker
#CMD ["/usr/sbin/sshd", "-D"]