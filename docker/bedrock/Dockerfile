FROM php:8.2-fpm

# Install essential packages
RUN apt-get update \
  && apt-get install -y \
    build-essential \
    curl \
    git \
    gnupg \
    less \
    nano \
    vim \
    unzip \
    zip \
  && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

# Install php extensions and related packages
ADD https://github.com/mlocati/docker-php-extension-installer/releases/latest/download/install-php-extensions /usr/local/bin/
RUN chmod +x /usr/local/bin/install-php-extensions && sync \
  && install-php-extensions \
    @composer \
    exif \
    gd \ 
    memcached \
    mysqli \
    pcntl \
    pdo_mysql \
    zip \
  && apt-get update \
  && apt-get install -y \
    gifsicle \
    jpegoptim \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    libmemcached-dev \
    locales \
    lua-zlib-dev \
    optipng \
    pngquant \
  && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean

# Install nginx, node, supervisor
RUN curl -sL https://deb.nodesource.com/setup_20.x | bash \
  && apt-get update \
  && apt-get install -y \
    nginx \
    nodejs \
    supervisor \
  && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
  && rm -rf /var/lib/apt/lists/* \
  && apt-get clean \
  && npm install -g yarn

# Configure nginx, php-fpm and supervisor

COPY nginx/nginx.conf /etc/nginx/nginx.conf
COPY nginx/sites-enabled /etc/nginx/conf.d
COPY nginx/sites-enabled /etc/nginx/sites-enabled
COPY php/fpm/pool.d /etc/php/8.2/fpm/pool.d
COPY supervisor/supervisord.conf /etc/supervisord.conf

# Download bedrock
RUN git clone https://github.com/roots/bedrock /var/www/bedrock

# Link theme
RUN ln -s /var/www/themes/main /var/www/bedrock/web/app/themes/main

# WordPress CLI
RUN curl -O https://raw.githubusercontent.com/wp-cli/builds/gh-pages/phar/wp-cli.phar \
  && chmod +x wp-cli.phar \
  && mv wp-cli.phar /usr/bin/_wp;
COPY wp.sh /var/www/wp.sh
RUN chmod +x /var/www/wp.sh \
  && mv /var/www/wp.sh /usr/bin/wp

# Installation helper
COPY bedrock-install.sh /var/www/bedrock-install.sh
RUN chmod +x /var/www/bedrock-install.sh

CMD ["/var/www/bedrock-install.sh"]