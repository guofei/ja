---
layout: post
title: wordpressとlet's encryptをdockerで構築する
categories: [Docker]
---
## ディレクトリを作る
{% highlight sh %}
mkdir myapp
cd myapp
{% endhighlight sh %}

## networkを作る
{% highlight sh %}
docker network create my-existing-network
{% endhighlight sh %}

## docker-compose.ymlファイルを作る
{% highlight yml %}
version: '2'
services:
  db:
    image: mysql:5.7
    volumes:
      - ./db:/var/lib/mysql
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: wordpress
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
  wordpress:
    depends_on:
      - db
    image: wordpress:latest
    volumes:
      - ./wp-content:/var/www/html/wp-content
    ports:
      - 80
    restart: always
    environment:
      WORDPRESS_DB_HOST: db:3306
      WORDPRESS_DB_PASSWORD: wordpress
      VIRTUAL_HOST: example.com
      LETSENCRYPT_HOST: example.com
      LETSENCRYPT_EMAIL: email@example.com
  nginx-proxy:
    image: jwilder/nginx-proxy
    restart: always
    ports:
      - 80:80
      - 443:443
    volumes:
      - ./certs:/etc/nginx/certs:ro
      - /etc/nginx/vhost.d
      - /usr/share/nginx/html
      - /var/run/docker.sock:/tmp/docker.sock:ro
  letsencrypt:
    # image: jrcs/letsencrypt-nginx-proxy-companion
    image: alastaircoote/docker-letsencrypt-nginx-proxy-companion
    restart: always
    volumes:
      - ./certs:/etc/nginx/certs:rw
      - /var/run/docker.sock:/var/run/docker.sock:ro
    volumes_from:
      - nginx-proxy
networks:
  default:
    external:
      name: my-existing-network
{% endhighlight yml %}

現時点で（2016年12月25日）jrcs/letsencrypt-nginx-proxy-companionを使うと、DeserializationError: Deserialization error: Wrong directory fieldsというエラーが出るので、最新のletsencrypt apiに対応してないみたいです。その代わりにalastaircoote/docker-letsencrypt-nginx-proxy-companionwを使います。

## appを起動
{% highlight sh %}
docker-compose up -d
{% endhighlight sh %}

参考：https://libertyseeds.ca/2016/09/20/Nginx-Let-s-Encrypt-and-Docker-Compose/

