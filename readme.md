# docker-nginx-php
## Supported tags and respective `Dockerfile` links
* `7.1-fpm`, `latest` [(7.1/Dockerfile)](https://github.com/socifi/docker-nginx-php/blob/master/7.1/Dockerfile)

[![dockeri.co](http://dockeri.co/image/socifi/nginx-php)](https://registry.hub.docker.com/socifi/nginx-php/)

[![dockeri.co](https://images.microbadger.com/badges/image/socifi/nginx-php.svg)](https://microbadger.com/images/socifi/nginx-php)

[![](https://images.microbadger.com/badges/version/socifi/nginx-php.svg)](https://microbadger.com/images/socifi/nginx-php "Get your own version badge on microbadger.com")

Based on the [Official PHP Docker image](https://store.docker.com/images/9c2c5426-0cca-4a30-a450-b2961541c6dc "official image") and [Offical Nginx Docker image](https://hub.docker.com/_/nginx/).
This image adds some extensions that are necessary in most common use cases for running PHP projects.

Nginx and PHP-FPM services running together in the one container. The webserver solution for dockerized PHP application. You should always use nginx-proxy in front of this container to manage load balancing, server names and SSL traffic (e.g let's encrypt).

This image actually only helps to run you application on port `80`. It simulates nodejs app server or golang web server.

It's based on *socifi/php* and official *nginx* images.

## Quickstart

1. Create own `Dockerfile` (*see Examples)*

2. Customise `default` vhost file (*see Examples*)
    PHP-FPM service is listening on `localhost:9000` by default

3. Add your code into `/app` folder

4. Build image (e.g.:)

    ```sh
    docker build -t namespace/application:latest .
    ```

5. Run the image (e.g.:)

    ```sh
    docker run -it --rm -p 80:80 namespace/application:latest
    ```


## Examples

### Dockerfile

project `Dockerfile` placed in the project root.

```sh
FROM socifi/nginx-php:7.1-fpm
LABEL maintainer "your name"
ADD etc/nginx/default /etc/nginx/sites-enabled/default
WORKDIR /app
COPY . /app
VOLUME /app
```

### Default

Example of default vhost configuration file placed in the `./etc/nginx/default`

```sh
# copy this file into socifi/nginx-php:7.1-fpm as /etc/nginx/sites-enabled/default

server {
	listen 80 default_server;
	listen [::]:80 default_server ipv6only=on;

	root /app;
	index index.php;
	
	error_log /dev/stdout info;
	access_log /dev/stdout;

	server_name localhost;

	location / {	
		try_files $uri $uri/ /index.html;
	}
	
	location ~ \.php[t]?$ {
		try_files	$uri =404;
		fastcgi_index index.php;	
		fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;	
		fastcgi_pass localhost:9000;
		include /etc/nginx/fastcgi_params;

	}
}
```

