FROM php:5.6-cli
MAINTAINER Michael A. Phang <mphangdev@gmail.com>
RUN apt-get -qqy update \
 && apt-get -qqy install git \
                         libcurl4-gnutls-dev \
                         libmcrypt-dev \
                         libpng12-dev \
                         libjpeg62-turbo-dev \
                         libxml2-dev \
                         libxslt-dev \
                         libssl-dev \
                         libbz2-dev \
                         libreadline-dev \
                         bzip2 \
  # Idiopathic rm failure; loop is workaround
 && until rm -rf /var/lib/apt/lists; do sleep 1; done \
 && useradd mage \
 && usermod -aG www-data mage
# setup phpbrew
ENV PHPBREW_HOME /opt/phpbrew
ENV PHPBREW_ROOT /opt/phpbrew
COPY phpbrew_install_extensions $PHPBREW_HOME/bin/phpbrew_install_extensions
COPY phpbrew_fpm_start $PHPBREW_HOME/bin/phpbrew_fpm_start
COPY phpbrew_build_env $PHPBREW_HOME/bin/phpbrew_build_env
RUN curl -L -O https://github.com/phpbrew/phpbrew/raw/1.22.5/phpbrew \
 && mv phpbrew $PHPBREW_HOME/bin/phpbrew \
 && chmod -R +rx $PHPBREW_HOME/bin \
 && export PATH=$PATH:$PHPBREW_HOME/bin \
 && phpbrew init \
 && phpbrew update --old \
 && bash $PHPBREW_HOME/bin/phpbrew_build_env \
 && echo "[ -f \"$PHPBREW_HOME/phpbrew_env\" ] && source $PHPBREW_HOME/phpbrew_env" >> ~/.bashrc
# build php
ENV MAGENTO_PHP_BREWED_VERSION "5.5.38"
RUN CXXFLAGS="-fPIC" $PHPBREW_HOME/bin/phpbrew install $MAGENTO_PHP_BREWED_VERSION \
                                                +default \
                                                +bcmath \
                                                +curl \
                                                +mcrypt \
                                                +mysql \
                                                +iconv \
                                                +fpm \
                                                +soap \
                                                +gd \
                                                -- \
                                                --with-gd=shared \
                                                --enable-gd-natf \
                                                --with-jpeg-dir=/usr/include \
                                                --with-png-dir=/usr/include
RUN bash $PHPBREW_HOME/bin/phpbrew_install_extensions
COPY php-fpm.conf $PHPBREW_HOME/php/php-$MAGENTO_PHP_BREWED_VERSION/etc/php-fpm.conf
COPY www-pool.conf $PHPBREW_HOME/php/php-$MAGENTO_PHP_BREWED_VERSION/etc/pool.d/www-pool.conf
COPY xdebug-enabler.ini /root/.phpbrew/php/php-$MAGENTO_PHP_BREWED_VERSION/var/db/xdebugenabler.ini
CMD ["$PHPBREW_HOME/bin/phpbrew_fpm_start"]
EXPOSE 9001
