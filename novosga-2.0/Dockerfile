#Build stage, useful for a better use of cache ;)
FROM composer:1.5.2 AS build

ENV NOVOSGA_VER=v2.0.0-BETA3 \
    NOVOSGA_MD5=d8d05e4cb386570f18b33339207529fa

ENV NOVOSGA_FILE=novosga.tar.gz \
    NOVOSGA_DIR=/var/www/html \
    APP_ENV=prod \
    NOVOSGA_URL=https://github.com/novosga/novosga/archive/${NOVOSGA_VER}.tar.gz \
    DATABASE_PASS=123456 \
    DATABASE_URL=mysql://root@127.0.0.1:3306/novosga?charset=utf8mb4&serverVersion=5.7

RUN set -xe \
    && mkdir -p $NOVOSGA_DIR && cd $NOVOSGA_DIR \
    && docker-php-ext-install pcntl \
    && curl -fSL ${NOVOSGA_URL} -o ${NOVOSGA_FILE} \
    && echo "${NOVOSGA_MD5}  ${NOVOSGA_FILE}" | md5sum -c \
    && tar -xz --strip-components=1 -f ${NOVOSGA_FILE} \
    && rm ${NOVOSGA_FILE} \
    && composer install --no-dev

FROM php:7.1-apache

RUN set -xe \
    && apt-get update \
    && apt-get install -y \
        libicu-dev \
        libxml2-dev \
        zlib1g-dev \
    && docker-php-ext-install \
        intl \
        pcntl \
        pdo \
        pdo_mysql \
        xml \
        zip \
    && apt-get remove -y --purge \
        postgresql-server-dev-all \
        libicu-dev \
        libxml2-dev \
        zlib1g-dev \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && a2enmod rewrite env \
    && echo 'session.save_path = "/tmp"' > /usr/local/etc/php/conf.d/sessionsavepath.ini \
    && echo 'date.timezone = ${TZ}' > /usr/local/etc/php/conf.d/datetimezone.ini

COPY --from=build /var/www/html /var/www/html

RUN set -xe \
    && chown -R www-data:www-data . \
    && sed -i 's|/var/www/html|/var/www/html/public|g' /etc/apache2/sites-available/000-default.conf \
    && echo \
'Options -MultiViews\n\
RewriteEngine On\n\
RewriteCond %{REQUEST_FILENAME} !-f\n\
RewriteRule ^(.*)$ index.php [QSA,L]\n\
PassEnv APP_ENV\n\
PassEnv DATABASE_URL\n\
PassEnv DATABASE_PASS' > public/.htaccess

#Set the default parameters
ENV APP_ENV=prod \
    NOVOSGA_ADMIN_USERNAME="admin" \
    NOVOSGA_ADMIN_PASSWORD="123456" \
    NOVOSGA_ADMIN_FIRSTNAME="Administrator" \
    NOVOSGA_ADMIN_LASTNAME="Global" \
    NOVOSGA_UNITY_NAME="My Unity" \
    NOVOSGA_UNITY_CODE="U01" \
    NOVOSGA_NOPRIORITY_NAME="Normal" \
    NOVOSGA_NOPRIORITY_DESCRIPTION="Normal service" \
    NOVOSGA_PRIORITY_NAME="Priority" \
    NOVOSGA_PRIORITY_DESCRIPTION="Priority service" \
    NOVOSGA_PLACE_NAME="Box"

    COPY start.sh /usr/local/bin

    CMD ["start.sh"]