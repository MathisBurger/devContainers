FROM --platform=linux/amd64 php:8.2-apache

ENV APACHE_DOCUMENT_ROOT /var/www/html/public
# https://getcomposer.org/doc/03-cli.md#composer-allow-superuser
ENV COMPOSER_ALLOW_SUPERUSER 1
ENV SASS_BINARY_PATH /usr/share/node-sass/binding.node
ENV PHP_EXTENSIONS "iconv curl pgsql mysqli pdo pdo_pgsql pdo_mysql intl zip soap gd bcmath exif"

RUN set -ex \
    && apt-get update && apt-get install --no-install-recommends -y \
       locales \
       wget \
       git \
       gpg \
       gpg-agent \
       dirmngr \
       openssh-client \
       rsync \
       libfreetype6-dev \
       libjpeg62-turbo-dev \
       libmcrypt-dev \
       libpng-dev \
       libcurl4-gnutls-dev \
       libpq-dev \
       libicu-dev \
       libxml2-dev \
       libzip-dev \
       nano \
       mariadb-client \
    && wget https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz \
    && tar xzf Python-2.7.18.tgz \
    && cd Python-2.7.18 \
    && ./configure --enable-optimizations \
    && make -j"$(nproc)" \
    && make altinstall \
    && ln -s /usr/local/bin/python2.7 /usr/bin/python2  \ 
	&& locale-gen de_DE.UTF-8 \
	&& update-locale \
    && mkdir ~/.gnupg \
    && chmod 0700 ~/.gnupg \
    && echo "disable-ipv6" >> ~/.gnupg/dirmngr.conf \
    && docker-php-ext-configure zip \
    && docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) $PHP_EXTENSIONS \
    && a2enmod rewrite \
    && a2enmod proxy \
    && a2enmod proxy_http \
    && a2enmod proxy_fcgi \
	&& curl --silent --show-error https://getcomposer.org/installer | php -- \
	   --force --install-dir=/usr/local/bin --filename=composer \
    && apt-get autoremove -y

RUN apt-get remove -y nodejs
RUN curl -L https://raw.githubusercontent.com/tj/n/master/bin/n -o /usr/local/bin/n \
&& chmod +x /usr/local/bin/n \
&& n 10.15.3 \
&& ln -sf /usr/local/n/versions/node/10.15.3/bin/node /usr/bin/node \
&& ln -sf /usr/local/n/versions/node/10.15.3/bin/npm /usr/bin/npm

WORKDIR /var/www/html

COPY vhost.conf /etc/apache2/sites-enabled/000-default.conf

EXPOSE 80
EXPOSE 9003

CMD ["apache2-foreground"]