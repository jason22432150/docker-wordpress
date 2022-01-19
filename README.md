# docker-wordpress
Use docker-compose create a complete docker wordpress container

``` sh
git clone https://github.com/jason22432150/docker-wordpress.git
mv docker-wordpress/ wordpress/
cd wordpress/
docker-compose up -d
```

This is docker-compose.yml
```diff
下方可修改部分
! MYSQL_ROOT_PASSWORD
! MYSQL_PASSWORD

+ ports:
+      - "2095:80"
```
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
****
If you want to Remove it
```sh
cd wordpress/
docker-compose down -v
```

docker-compoes wordpress Nginx reverse proxy


https://github.com/jason22432150/Nginx-reverse-proxy

```nginx
server {
    listen 80;
    listen [::]:80;
    index index.html;
    server_name miku-izayoi-wordpress.tk;    # 若沒有domain的話，該行可以拿掉

    location / {
        proxy_pass http://localhost:2095;
        # 把 IP、Protocol 等 header 都一起送給反向代理的 server
        proxy_redirect     off;
        proxy_set_header   Host $host;
        proxy_set_header   X-Forwarded-Host $server_name;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $http_x_forwarded_proto;
    }
}
```
