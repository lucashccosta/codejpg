FROM php:8.1-fpm

ARG BUILD_ARGUMENT_ENV=dev
ARG HOST_UID=1000
ARG HOST_GID=1000

ENV APP_ENV=$BUILD_ARGUMENT_ENV
ENV APP_HOME /var/www/html
ENV USERNAME=www-data

RUN apt-get update \ 
    && apt-get upgrade -y \ 
    && apt-get install -y libzip-dev libonig-dev libpq-dev \
    && rm -rf /tmp/* \
    && rm -rf /var/list/apt/* \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get clean

RUN docker-php-ext-install \ 
    bcmath \
    mbstring \
    pdo \
    pdo_pgsql \
    sockets \
    zip

RUN mkdir -p $APP_HOME/public \
    && mkdir -p /home/$USERNAME && chown $USERNAME:$USERNAME /home/$USERNAME \
    && usermod -o -u $HOST_UID $USERNAME -d /home/$USERNAME \
    && groupmod -o -g $HOST_GID $USERNAME \
    && chown -R ${USERNAME}:${USERNAME} $APP_HOME

RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer \
    && chmod +x /usr/local/bin/composer

WORKDIR $APP_HOME
USER ${USERNAME}
COPY --chown=${USERNAME}:${USERNAME} ./src/ $APP_HOME/
COPY --chown=${USERNAME}:${USERNAME} ./src/.env.example $APP_HOME/.env

RUN if [ "$APP_ENV" = "dev" ] ; then \
        composer clearcache \
        && composer install --no-interaction --optimize-autoloader --no-progress; \
    else \
        composer clearcache \
        && composer install --no-dev --no-interaction --optimize-autoloader --no-progress; \
    fi

EXPOSE 9000
ENTRYPOINT ["php-fpm"]