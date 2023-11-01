FROM php:8.2-fpm

# Arguments defined in docker-compose.yml
ARG user
ARG uid

RUN apt-get update && apt-get install --yes curl

ARG NODE_VERSION=18 # NodeJS
# install NodeJS
RUN apt-get update -yq \
    && apt-get install -yq ca-certificates curl gnupg \
    && mkdir -p /etc/apt/keyrings \
    && curl -fsSL https://deb.nodesource.com/gpgkey/nodesource-repo.gpg.key | gpg --dearmor -o /etc/apt/keyrings/nodesource.gpg \
    && echo "deb [signed-by=/etc/apt/keyrings/nodesource.gpg] https://deb.nodesource.com/node_$NODE_VERSION.x nodistro main" | tee /etc/apt/sources.list.d/nodesource.list \
    && apt-get update -yq \
    && apt-get install nodejs -yq \
    && npm install -g npm

# Install system dependencies
RUN apt-get update && apt-get install -y \
    git \
    libpng-dev \
    libonig-dev \
    libxml2-dev \
    libfreetype-dev \
    libjpeg62-turbo-dev \
    libpng-dev \
    libxslt1-dev \
    libzip-dev \
    zip \
    gzip \
    unzip\
    vim

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

RUN pecl channel-update pecl.php.net && pecl install \
    redis \
    xdebug \
    opcache \
    && pecl clear-cache \
    && rm -rf /tmp/pear \
    && docker-php-ext-enable redis xdebug opcache

# Install PHP extensions
RUN docker-php-ext-configure gd --with-freetype --with-jpeg \
    && docker-php-ext-install -j$(nproc) \ 
        bcmath \
        dom \
        gd \
        intl \
        soap \
        sockets \
        xsl \
        zip

# Get latest Composer
RUN curl -sS https://getcomposer.org/installer | \
  php -- --install-dir=/usr/local/bin --filename=composer

COPY conf/ $PHP_INI_DIR/conf.d/

# Create system user to run Composer and Artisan Commands
RUN useradd -G www-data,root -u $uid -d /home/$user $user
RUN mkdir -p /home/$user/.composer && \
    chown -R $user:$user /home/$user

# Set working directory
WORKDIR /var/www

USER $user
