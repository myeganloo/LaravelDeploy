FROM php:8.3-fpm

WORKDIR /var/www

RUN curl -sL https://deb.nodesource.com/setup_14.x | bash - && \
    curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

RUN set -eux; \
    apt-get update && apt-get upgrade -y && \
    apt-get install -y --no-install-recommends \
        curl \
        libzip-dev \
        nodejs \
        git \
        libmemcached-dev \
        libz-dev \
        libpq-dev \
        libjpeg-dev \
        libpng-dev \
        libfreetype6-dev \
        libssl-dev \
        libwebp-dev \
        libxpm-dev \
        libmcrypt-dev \
        libonig-dev \
        librabbitmq-dev \
        libmongoc-dev \
        supervisor \
        xz-utils \
    && pecl install \
        amqp \
        mongodb \
        memcached \
        apcu \
        redis \
        xdebug \
    && docker-php-ext-enable \
        amqp \
        mongodb \
        memcached \
        apcu \
        redis \
        xdebug \
    && docker-php-ext-configure zip \
    && docker-php-ext-install \
        sockets \
        exif \
        opcache \
        pdo_mysql \
        pcntl \
        zip \
    && docker-php-ext-configure gd --with-jpeg --with-webp --with-xpm --with-freetype \
    && docker-php-ext-install gd \
    && rm -rf /var/lib/apt/lists/* /var/cache/apt/archives/*


COPY wkhtmltox_0.12.6.1-3.bookworm_amd64.deb /tmp/
RUN echo "deb http://archive.debian.org/debian/ buster main" > /etc/apt/sources.list.d/debian-archive.list && \
    apt-get update && apt-get install -y --no-install-recommends \
    fontconfig \
    libssl1.1 \
    libxext6 \
    libxrender1 \
    xfonts-75dpi \
    xfonts-base && \
    ls /tmp/ && \
    dpkg -i /tmp/wkhtmltox_0.12.6.1-3.bookworm_amd64.deb || (echo "Error installing wkhtmltox.deb"; exit 1) && \
    rm -rf /tmp/wkhtmltox_0.12.6.1-3.bookworm_amd64.deb && \
    rm -rf /var/lib/apt/lists/* /var/cache/apt/archives/* && \
    wkhtmltopdf --version

RUN groupadd -g 1000 www && \
    useradd -u 1000 -ms /bin/bash -g www www

USER www

EXPOSE 9000

COPY supervisord.conf /etc/supervisor/supervisord.conf
COPY supervisord-l.conf /etc/supervisor/conf.d/supervisord-l.conf

CMD ["supervisord"]
#ENTRYPOINT ["supervisord", "-n", "-c", "/etc/supervisor/conf.d/supervisord-l.conf"]
