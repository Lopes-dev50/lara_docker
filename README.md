# Laravel + MariaDB + PHPMyAdmin + Docker

## Configuration files

### Dockerfile

```
FROM php:7.4.1-apache-buster
RUN apt-get update && apt-get install -y libzip-dev
RUN docker-php-ext-install zip pdo pdo_mysql
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer
RUN mkdir /var/www/laravel/
WORKDIR /var/www/laravel/
RUN composer global require laravel/installer
ENV PATH="/root/.composer/vendor/bin:${PATH}"
COPY laravel.conf /etc/apache2/sites-available
RUN a2ensite laravel.conf
RUN a2enmod rewrite
```

### Docker Compose

```
version: "3.3"
services: 
    laravel:
        build: .
        working_dir: /var/www/laravel/
        volumes: 
            - ./app/laravel:/var/www/laravel/
        ports: 
            - 8080:80
    db:
        image: mariadb
        restart: always
        environment: 
            MYSQL_DATABASE: laravel
            MYSQL_ALLOW_EMPTY_PASSWORD: 1
        volumes:
            - ./app/data:/var/lib/mysql
        ports: 
            - 3306:3306
    phpmyadmin:
        image: phpmyadmin/phpmyadmin
        restart: always
        depends_on:
            - db
        ports:
            - 8081:80  
```

### Virtual host configuration (laravel.conf)

```
<VirtualHost *:80>
    ServerName laravel.local

    ServerAdmin webmaster@laravel.local
    DocumentRoot /var/www/laravel/public

    <Directory /var/www/laravel/>
        AllowOverride All
    </Directory>

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
```

## How to run

```
git clone https://github.com/AFelipeTrujillo/laravel-mariadb-docker.git
cd laravel-mariadb-docker
docker-compose up
```

### Create a laravel project

```
docker-compose exec web laravel new
```

### Edit Hosts file
Add the next line in hosts file
```
127.0.0.1 laravel.local
```

### Open app
**Server** is running on http://laravel.local:8080

**PHPMyAdmin** is running on  http://laravel.local:8081

### How to execute artisan?
```
docker-compose exec web php artisan
```
