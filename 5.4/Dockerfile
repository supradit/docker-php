FROM        debian:wheezy-slim
MAINTAINER  Emmanuel Dyan <emmanueldyan@gmail.com>

ARG         DEBIAN_FRONTEND=noninteractive

# Set a default conf for apt install
RUN         echo 'apt::install-recommends "false";' > /etc/apt/apt.conf.d/no-install-recommends
# Install packages
RUN         apt-get update && \
            apt-get upgrade -y && \
            apt-get install -y \
                iptables \
                # All PHP Packages
                php-apc \
                php5-cli \
                php5-curl \
                php5-ffmpeg \
                php5-fpm \
                php5-gd \
                php5-geoip \
                php5-imagick \
                php5-imap \
                php5-intl \
                php5-json \
                php5-ldap \
                php5-mcrypt \
                php5-memcached \
                php5-mysql \
                php5-odbc \
                php5-pgsql \
                php5-sqlite \
                php5-tidy \
                php5-xdebug \
                php5-xsl && \
            # Now Build
            apt-get install -y \
                build-essential php-pear php5-dev libcurl4-openssl-dev pkg-config && \
            pecl channel-update pecl.php.net && \
            pecl install -f xhprof-beta mongo && \
            rm -Rf /tmp/pear && \
            apt-get purge -y build-essential php-pear php5-dev libcurl4-openssl-dev pkg-config && \
            # Clean
            apt-get autoremove -y && \
            apt-get autoclean && \
            apt-get clean && \
            rm -Rf /build /root/.bashrc /root/.profile /var/lib/apt/lists/* /usr/share/man/* /usr/share/doc/* /var/cache/* /var/log/*

RUN         echo "extension=xhprof.so" > /etc/php5/mods-available/xhprof.ini && \
            php5enmod xhprof && \
            echo "extension=mongo.so" > /etc/php5/mods-available/mongo.ini && \
            php5enmod mongo

COPY        php-cli.ini    /etc/php5/cli/conf.d/30-custom-php.ini
COPY        php-fpm.ini    /etc/php5/fpm/conf.d/30-custom-php.ini
COPY        www.conf       /etc/php5/fpm/pool.d/


# For custom Configuration that comes from outside (via a docker compose mount)
RUN         mkdir /etc/php5/fpm/user-conf.d
RUN         echo "; Default empty file" > /etc/php5/fpm/user-conf.d/example.conf
RUN         mkdir /var/log/php


# Create home for www-data
RUN         mkdir /home/www-data
RUN         chown www-data:www-data /home/www-data
RUN         usermod -d /home/www-data www-data


COPY        run.sh     /run.sh
RUN         chmod +x    /run.sh

ENV         ENVIRONMENT dev
ENV         FPM_UID 33
ENV         FPM_GID 33

EXPOSE      9000

CMD         ["/run.sh"]
