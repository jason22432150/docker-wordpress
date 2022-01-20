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
! MYSQL_ROOT_PASSWORD: password
! MYSQL_PASSWORD: password
! WORDPRESS_DB_PASSWORD: password

+ ports:
+      - "2095:80"
```
```yml
version: "3.9"

services:
  db:
    image: mysql:5.7
    volumes:
      - db_data:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: password
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: password

  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - wordpress_data:/var/www/html
      - ./uploads.ini:/usr/local/etc/php/conf.d/uploads.ini
    ports:
      - "2095:80"
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: password
      WORDPRESS_DB_NAME: wordpress
volumes:
  db_data: {}
  wordpress_data: {}
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
    server_name your-domain.name;    # 若沒有domain的話，該行可以拿掉

    location / {
        proxy_pass http://localhost:2095;
        # 把 IP、Protocol 等 header 都一起送給反向代理的 server
        proxy_redirect   off;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-Host $server_name;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $http_x_forwarded_proto;
    }
}
```

Nignx設定有一個 client_max_body_size 的值，這個值負責定義Client可以請求的內容大小，當請求的大小超過伺服器可以處理的限度，那伺服器就會關閉連線以防止客戶端繼續傳送該請求

而這個值，如果沒有定義的話，預設值正是1MB

這個設定參數可以被設定在http、server、location的區塊中，如果想要讓這個設定對整個伺服器都生效的話，只要把他設定在http區塊裡面就可以了！

可以設定上限值，設定如下：
```nginx
http {
    ...

    # 設定請求內容大小上限值為 5MB
    client_max_body_size 5m;

    ...
}
```
也可以定義無上限：
```nginx
http {
    ...

    # 設定請求內容大小無上限
    client_max_body_size 0;

    ...
}
```
