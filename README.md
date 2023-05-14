# A PHP fpm Health Check Docker Container

An alpine based PHP-fpm docker image with integrated healthcheck.

- [Usage](#usage)
- [Credits and License](#credits)

## Usage

To run this container in combination with nginx you need these two files:

- `docker-compose.yml`
- `conf.d/php.conf`

The two files below or in this repo.

### Warning

PHP will not work without `conf.d/php.conf`, create it before you deploy the container!

---

`docker-compose.yml` :

```yml
version: "3.5"

services:
  nginx:
    image: nginx:alpine
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "--fail", "http://localhost"]
      start_period: 5s
      interval: 5s
      timeout: 5s
      retries: 15
    networks:
      - backend
    ports:
      - 80:80
    volumes:
      - ./www:/code
      - ./conf.d:/etc/nginx/conf.d
    depends_on:
      php:
        condition: service_healthy
  php:
    image: ghcr.io/schreier-development/php-fpm-healthcheck:latest
    restart: unless-stopped
    networks:
      - backend
    volumes:
      - ./www:/code

networks:
  backend:
    external: false
    driver: bridge
```

`conf.d/php.conf` :

```yml
server {
    index index.php index.html;
    server_name nginx;
    error_log  /var/log/nginx/error.log;
    access_log /var/log/nginx/access.log;
    root /code;

#========== php config
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_split_path_info ^(.+\.php)(/.+)$;
        fastcgi_pass php:9000;
        fastcgi_index index.php;
        include fastcgi_params;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        fastcgi_param PATH_INFO $fastcgi_path_info;
        }
}
```

## Credits

php-fpm-healtcheck by [Renato Mefi](https://github.com/renatomefi)

Distributed under [MIT License](LICENSE)
