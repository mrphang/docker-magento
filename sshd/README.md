## Magento Docker Tools Container

You can connect to this container to [install Magento](#installing-magento),
or to get access to the filesystem for running tools.
You don't even have to build it yourself.

##### Note:

This image is a utility to aid in development only. Security took a backseat that was then ejected and left stranded in a galaxy far aw... Just don't deploy anywhere security could or should be concern.

### Pull It

    docker pull mrphang/magento_sshd:latest

### Or, Build It Yourself

    docker build --rm sshd

### Run It

##### Note: Container Names

This project is more useful when you can create multiple docker containers for
various Magento projects. To do that, you will need to vary the names of those
projects using either the `-p` flag, the
[`COMPOSE_PROJECT_NAME`](http://docs.docker.com/compose/cli/#compose95project95name)
or the basename of the current working directory (but only alphanumeric
characters are included).

To get started quickly with a consistent name based on the current directory,
run the following:

    export COMPOSE_PROJECT_NAME
    COMPOSE_PROJECT_NAME="${PWD##*/}"
    COMPOSE_PROJECT_NAME="${COMPOSE_PROJECT_NAME//[^[:alnum:]]}"

The below commands all assume you have `COMPOSE_PROJECT_NAME` in the current
environment.

    docker run -d --link "${COMPOSE_PROJECT_NAME}"_db_1:db_1 \
               --volumes-from "${COMPOSE_PROJECT_NAME}"_data_1 \
               mrphang/magento_sshd

#### Shell Access for Arbitrary Commands

The below commands assume you have docker-compose installed. To login as `mage`, the password is `mage`. To login as `root`, the password is `root`.

    ssh -p ${$(docker-compose port sshd 22)#'0.0.0.0:'} mage@localhost

if you want to make system changes to the container or run as root:

    ssh -p ${$(docker-compose port sshd 22)#'0.0.0.0:'} root@localhost

#### Magento Reindex

      ssh -p ${$(docker-compose port sshd 22)#'0.0.0.0:'} mage@localhost \
               'php shell/indexer.php reindexall'

#### Install Magento

After setting up the service containers as described in the
[main README](https://github.com/kojiromike/docker-magento/blob/master/README.md),
you can use this container to install Magento.

##### Choose a Magento

Either untar a Magento into /srv/magento or provide a tarball mounted at
/magento.tar as in the example below. Link the MySQL service and data volume
containers, and optionally set `MAGENTO_HOST` to the hostname or ip address
of the Docker host.

You can do this with:

    docker run -d --link "${COMPOSE_PROJECT_NAME}"_db_1:db_1 \
               --volumes-from "${COMPOSE_PROJECT_NAME}"_data_1 \
               --volume /path/to/magento.tar:/magento.tar \
               --volume /path/to/magento-sample-data.tar:/sample.tar \ # Optional
               --env MAGENTO_HOST=$(boot2docker ip) \ # Optional
               mrphang/magento_sshd

Once the service is up, run:

    ssh -p ${$(docker-compose port sshd 22)#'0.0.0.0:'} mage@localhost \
        '/usr/local/bin/install_magento'

## Available Tools:

- [MySQL Client](http://dev.mysql.com/doc/refman/5.6/en/programs-client.html)
- [PHP Copy/Paste Detector](https://github.com/sebastianbergmann/phpcpd)
- [PHP Depend](http://pdepend.org/)
- [PHP Mess Detector](http://phpmd.org/)
- [PHPUnit](https://phpunit.de)
- [PHP\_CodeSniffer](https://github.com/squizlabs/PHP_CodeSniffer)
- [cURL](http://curl.haxx.se/)
- [composer](https://getcomposer.org/)
- [git](http://git-scm.com/)
- [modman](https://github.com/colinmollenhour/modman)
- [n98-magerun](https://github.com/netz98/n98-magerun)
- [phpDocumentor](http://www.phpdoc.org/)
- [phploc](https://github.com/sebastianbergmann/phploc)
- [vim](http://www.vim.org/about.php)
- [xdebug](http://www.xdebug.org/)
- [jq](https://stedolan.github.io/jq/)

## XDebug Notes

XDebug can slow down PHP significantly, so it is not enabled by default.
To enable xdebug, you can specify it on the php commandline. For example,
to generate PHPUnit code coverage reports:

    php -d zend_extension=/usr/local/lib/php/extensions/no-debug-non-zts-20121212/xdebug.so \
        vendor/bin/phpunit --coverage-html coverage-html
