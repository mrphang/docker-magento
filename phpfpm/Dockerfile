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
 && curl -L -O https://github.com/phpbrew/phpbrew/raw/1.22.5/phpbrew \
 && chmod +x phpbrew \
 && mv phpbrew /usr/local/bin/phpbrew \
 && phpbrew init \
 && phpbrew update --old \
 && echo "[ -f \"~/.phpbrew/bashrc\" ] && source ~/.phpbrew/bashrc" >> ~/.bashrc
# build php
ENV MAGENTO_PHP_BREWED_VERSION "5.4.43"
COPY phpbrew_fpm_start /usr/local/bin/
COPY php-fpm.conf /root/.phpbrew/php/php-$MAGENTO_PHP_BREWED_VERSION/etc/php-fpm.conf
COPY www-pool.conf /root/.phpbrew/php/php-$MAGENTO_PHP_BREWED_VERSION/etc/pool.d/www-pool.conf
RUN chmod +x /usr/local/bin/phpbrew_fpm_start \
 && phpbrew install $MAGENTO_PHP_BREWED_VERSION +default \
                                                +bcmath \
                                                +curl \
                                                +mcrypt \
                                                +mysql \
                                                +iconv \
                                                +fpm \
                                                -- \
                                                --with-jpeg-dir=/usr/include/
COPY phpbrew_install_extensions /usr/local/bin/
RUN chmod +x /usr/local/bin/phpbrew_install_extensions \
 && bash phpbrew_install_extensions
CMD ["/usr/local/bin/phpbrew_fpm_start"]
EXPOSE 9001
