# docker-wordpress
Use docker-compose create a complete docker wordpress container

``` sh

git clone https://github.com/jason22432150/docker-wordpress.git

docker-compose up -d

```

This is docker-comporse.yml
```yml
version: "3"
services:

   db:
     image: mysql:8.0
     command:
      - --default_authentication_plugin=mysql_native_password
      - --character-set-server=utf8mb4
      - --collation-server=utf8mb4_unicode_ci
     volumes:
       - db_data:/var/lib/mysql
     restart: always
     environment:
       MYSQL_ROOT_PASSWORD: somewordpress
       MYSQL_DATABASE: wordpress
       MYSQL_USER: wordpress
       MYSQL_PASSWORD: wordpress

   wordpress:
     depends_on:
       - db
     image: wordpress:latest
     # set the port here
     ports:
       - "2095:80"
     restart: always
     volumes:
       - /user/wordpress/uploads.ini:/usr/local/etc/php/conf.d/uploads.ini
     environment:
       WORDPRESS_DB_HOST: db:3306
       WORDPRESS_DB_USER: wordpress
       WORDPRESS_DB_PASSWORD: wordpress
volumes:
  db_data:
```
