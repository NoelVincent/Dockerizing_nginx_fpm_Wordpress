# Dockerizing_nginx_fpm_Wordpress

```sh
# docker ps
CONTAINER ID   IMAGE                         COMMAND                  CREATED         STATUS         PORTS                                   NAMES
2663bd9ef783   wordpress:php7.4-fpm-alpine   "docker-entrypoint.s…"   7 seconds ago   Up 5 seconds   9000/tcp                                wordpress
f1f73bd1f886   nginx:1.20-alpine             "/docker-entrypoint.…"   8 seconds ago   Up 6 seconds   0.0.0.0:8082->80/tcp, :::8082->80/tcp   nginx
1421e85f08f3   mysql:latest                  "docker-entrypoint.s…"   8 seconds ago   Up 6 seconds   3306/tcp, 33060/tcp                     mysql
```

```sh
/root/wordsite/
├── conf
│   └── website.conf
└── docker-compose.yml
```
