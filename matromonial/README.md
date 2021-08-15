# README.md
### i don't know  -in Dockerfile
- USER

### Dockerfile
```JS
FROM php:7.3-fpm

# Copy composer.lock and composer.json
COPY backend/composer.lock backend/composer.json /var/www/

# Set working directory
WORKDIR /var/www

# Install dependencies
RUN apt-get update && apt-get install -y \
    build-essential \
    mariadb-client \
    libpng-dev \
    libjpeg62-turbo-dev \
    libfreetype6-dev \
    libzip-dev \
    locales \
    zip \
    jpegoptim optipng pngquant gifsicle \
    vim \
    unzip \
    git \
    curl

# Clear cache
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Install extensions
RUN docker-php-ext-install pdo_mysql mbstring zip exif pcntl
RUN docker-php-ext-configure gd --with-gd --with-freetype-dir=/usr/include/ --with-jpeg-dir=/usr/include/ --with-png-dir=/usr/include/
# RUN docker-php-ext-install gd

# Install Postgre PDO
RUN apt-get update && apt-get install -y libpq-dev && docker-php-ext-install pdo pdo_pgsql

# Install composer
RUN curl -sS https://getcomposer.org/installer | php -- --install-dir=/usr/local/bin --filename=composer

# Add user for laravel application
RUN groupadd -g 1000 www
RUN useradd -u 1000 -ms /bin/bash -g www www

# Copy existing application directory contents
COPY . /var/www

# Copy existing application directory permissions
COPY --chown=www:www . /var/www

# Change current user to www
USER www

# Expose port 9000 and start php-fpm server
EXPOSE 9000
CMD ["php-fpm"]



```


### Dockerfile-website
```JS
FROM node:12.22.1-alpine3.10

WORKDIR /app

COPY frontend/package*.json ./

RUN npm install

# # add `/app/node_modules/.bin` to $PATH
# ENV PATH /app/node_modules/.bin:$PATH

# # install and cache app dependencies
# COPY frontend/package.json /app/package.json
# RUN npm install
# # RUN npm install @vue/cli@3.7.0 -g

EXPOSE 8080

CMD ["npm", "run", "serve"]

```



### i don't know  -in docker-compose.yml
- context ?
- container_name ? 
- restart ?
- tty ?
- environment ?
- volumes ?
- networks ? 


```JS
version: '3.3'
services:

  #PHP Service
  app:
    build:
      context: .
      dockerfile: docker/Dockerfile
    container_name: matrimonial-assist-api
    restart: always
    tty: true
    environment:
      SERVICE_NAME: matrimonial-assist-api
      SERVICE_TAGS: dev
    working_dir: /var/www
    volumes:
       - ./backend:/var/www
       - ./docker/php/local.ini:/usr/local/etc/php/conf.d/local.ini
    networks:
      - app-network

  #Nginx Service
  webserver:
    image: nginx:alpine
    container_name: matrimonial-assist-webserver
    restart: always
    tty: true
    ports:
      - "8888:80"
        #  - "4431:443"
    volumes:
      - ./backend:/var/www
      - ./docker/nginx/conf.d/:/etc/nginx/conf.d/
    networks:
      - app-network

  #MySQL Service
  db:
    image: mysql:8.0.17
    container_name: matrimonial-assist-db
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    tty: true
    ports:
      - "3307:3306"
    environment:
      MYSQL_DATABASE: matrimonial-assist
      MYSQL_ROOT_PASSWORD: 123456@Mat
      SERVICE_TAGS: dev
      SERVICE_NAME: mysql
    volumes:
      - dbdata:/var/lib/mysql
    networks:
      - app-network

  #adminer for db access
  adminer:
    image: adminer:latest
    restart: unless-stopped
    container_name: matrimonial-assist-adminer
    ports:
      - 8800:8080

  #Frontend
  website:
    build:
      context: .
      dockerfile: docker/Dockerfile-website
    container_name: matrimonial-assist-website
    restart: always
    volumes:
      - ./frontend:/app
      - /app/node_modules
    ports:
        - 8080:8080
    networks:
      - app-network

#Docker Networks
networks:
  app-network:
    driver: bridge

#Volumes
volumes:
  dbdata:
    driver: local 








```
