FROM wordpress:5.9-php8.1-fpm

COPY ./php.ini /usr/local/etc/php/php.ini


RUN apt-get update
RUN apt-get install --no-install-recommends -y tidy csstidy nano netcat 

RUN mkdir -p /usr/src/php/ext


# install ssmtp
RUN apt-get install -y ssmtp && \
    apt-get clean

# SSMTP settings
COPY ssmtp.conf /etc/ssmtp/ssmtp.conf

# PHP mail settings
RUN echo 'sendmail_path = "/usr/sbin/ssmtp -t -i"' > /usr/local/etc/php/conf.d/mail.ini



# install ssmtp
RUN apt-get install -y ssmtp && \
    apt-get clean

# SSMTP settings
COPY ssmtp.conf /etc/ssmtp/ssmtp.conf
# PHP mail settings
RUN echo 'sendmail_path = "/usr/sbin/ssmtp -t -i"' > /usr/local/etc/php/conf.d/mail.ini


# Install memcached

RUN apt-get install -y libpq-dev libmemcached-dev zlib1g-dev && \
    curl -o memcached.tgz -SL https://pecl.php.net/get/memcache-8.0.tgz && \
        tar -xf memcached.tgz -C /usr/src/php/ext/ && \
        echo 'extension=memcached.so' >> /usr/local/etc/php/conf.d/memcached.ini && \
        rm memcached.tgz && \
        mv /usr/src/php/ext/memcache-8.0 /usr/src/php/ext/memcached

RUN { \
        echo 'opcache.memory_consumption=128'; \
        echo 'opcache.interned_strings_buffer=8'; \
        echo 'opcache.max_accelerated_files=4000'; \
        echo 'opcache.revalidate_freq=2'; \
        echo 'opcache.fast_shutdown=1'; \
        echo 'opcache.enable_cli=1'; \
    } > /usr/local/etc/php/conf.d/opcache-recommended.ini





# Install needed php extensions: memcache
#
#RUN apt-get install --no-install-recommends -y unzip libssl-dev libpcre3 libpcre3-dev libzip-dev && \
#    cd /usr/src/php/ext/ && \
#    curl -sSL -o php8.zip https://github.com/websupport-sk/pecl-memcache/archive/NON_BLOCKING_IO_php8.zip && \
#    unzip php8.zip && \
#    mv pecl-memcache-NON_BLOCKING_IO_php8 memcache && \
#    docker-php-ext-configure memcache --with-php-config=/usr/local/bin/php-config && \
#    docker-php-ext-install memcache && \
#    echo "extension=memcache.so" > /usr/local/etc/php/conf.d/ext-memcache.ini && \
#    rm -rf /tmp/pecl-memcache-php8 php8.zip


# Install needed php extensions: zip
#
RUN apt-get install -y libzip-dev unzip && \
    curl -o zip.tgz -SL http://pecl.php.net/get/zip-1.15.1.tgz && \
        tar -xf zip.tgz -C /usr/src/php/ext/ && \
        rm zip.tgz && \
        mv /usr/src/php/ext/zip-1.15.1 /usr/src/php/ext/zip

RUN docker-php-ext-install memcached
#RUN docker-php-ext-install memcache
RUN docker-php-ext-install zip



# Install w3 total cache
RUN cd /usr/src/wordpress/wp-content/plugins && \
    curl -o w3-total-cache.2.2.1.zip -L https://downloads.wordpress.org/plugin/w3-total-cache.2.2.1.zip && \
    unzip -o w3-total-cache.2.2.1.zip && \
    chown -R www-data:www-data w3-total-cache && \
    rm -f w3-total-cache.2.2.1.zip

# Enable Wordpress Caching
RUN cd /usr/src/wordpress && ls -l && \
    sed -ri -e "s#(<\?php)#\\1\ndefine('WP_CACHE', 'true');#g" wp-config-sample.php


# Cleanup
RUN rm -rf /var/lib/apt/lists/*


# ENTRYPOINT resets CMD
ENTRYPOINT ["docker-entrypoint.sh"]
CMD ["php-fpm"]

