# Docker compose - Creating NGINX-FPM-Wordpress site.
Here, we will be creating containers that need to create/run a wordpress site.

# Docker
Docker is an open platform for developing, shipping, and running applications. Docker enables you to separate your applications from your infrastructure so you can deliver software quickly. 

Docker provides the ability to package and run an application in a loosely isolated environment called a container. The isolation and security allow you to run many containers simultaneously on a given host. Containers are lightweight and contain everything needed to run the application, so you do not need to rely on what is currently installed on the host. You can easily share containers while you work too.
You can learn more about the same in the [website.](https://docs.docker.com/get-started/overview/)

# What Is a Container
A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.
You can learn more about the same in the [website.](https://www.docker.com/resources/what-container)


# Docker compose
Compose is a tool for defining and running multi-container Docker applications. With Compose, you use a YAML file to configure your application’s services. Then, with a single command, you create and start all the services from your configuration.
You can learn more about the same in the [website.](https://docs.docker.com/compose/)


# Installing Docker
> Here I am using AWS Amazon linux server
```sh
amazon-linux-extras install docker -y
systemctl restart docker.service
systemctl enable docker.service
```
Please see the [website](https://docs.docker.com/engine/install/) for more information.

# Installing Docker compose
```sh
curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

chmod +x /usr/local/bin/docker-compose
```
Please see the [website](https://docs.docker.com/compose/install/) for more information.

# File structure
```sh
/root/wordsite/
├── conf
│   └── website.conf
└── docker-compose.yml
```
# Configuration file
```sh
vim website.conf
```
```sh
server {
  listen 80;
  listen [::]:80;
  access_log off;
  root /var/www/html;
  index index.php;
  server_name example.com;
  server_tokens off;
  location / {
    # First attempt to serve request as file, then
    # as directory, then fall back to displaying a 404.
    try_files $uri $uri/ /index.php?$args;
  }
  # pass the PHP scripts to FastCGI server listening on wordpress:9000
  location ~ \.php$ {
    fastcgi_split_path_info ^(.+\.php)(/.+)$;
    fastcgi_pass wordpress:9000;
    fastcgi_index index.php;
    include fastcgi_params;
    fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
    fastcgi_param SCRIPT_NAME $fastcgi_script_name;
  }
}
```
# Docker Compose
> Note: The compose file should have the name and the extension as docker-compose.yml
```sh
---
version: '3'
services:
  web:
    image: nginx:1.20-alpine
    container_name: nginx
    restart: always
    ports:
      - "8082:80"
    volumes:
      - wpvol:/var/www/html/
      - ./conf/website.conf:/etc/nginx/conf.d/default.conf
    networks:
      - wordsite

  mysql_db:  
    image: mysql:latest
    container_name: mysql
    restart: always
    volumes:
      - dbvol:/var/lib/mysql/
    networks:
      - wordsite
    environment:
      - MYSQL_ROOT_PASSWORD=root@123
      - MYSQL_DATABASE=wp
      - MYSQL_USER=wp
      - MYSQL_PASSWORD=wp

  wordfpm:
    image: wordpress:php7.4-fpm-alpine
    container_name: wordpress
    restart: always
    volumes:
      - wpvol:/var/www/html
    depends_on:
      - mysql_db
    environment:
      - WORDPRESS_DB_HOST=mysql
      - WORDPRESS_DB_NAME=wp
      - WORDPRESS_DB_USER=wp
      - WORDPRESS_DB_PASSWORD=wp
    expose:
      - 9000
    networks:
      - wordsite

volumes:
  dbvol:
  wpvol:
networks:
  wordsite: 
```
## Execution
- Running a syntax check
```sh
docker-compose config
```
- Executing the yaml file
```sh
docker-compose up -d
```
```sh
# docker ps
CONTAINER ID   IMAGE                         COMMAND                  CREATED         STATUS         PORTS                                   NAMES
2663bd9ef783   wordpress:php7.4-fpm-alpine   "docker-entrypoint.s…"   7 seconds ago   Up 5 seconds   9000/tcp                                wordpress
f1f73bd1f886   nginx:1.20-alpine             "/docker-entrypoint.…"   8 seconds ago   Up 6 seconds   0.0.0.0:8082->80/tcp, :::8082->80/tcp   nginx
1421e85f08f3   mysql:latest                  "docker-entrypoint.s…"   8 seconds ago   Up 6 seconds   3306/tcp, 33060/tcp                     mysql
```
