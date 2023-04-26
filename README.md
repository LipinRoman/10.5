# Домашнее задание к занятию 10.5 «Балансировка нагрузки. HAProxy/Nginx» - Липин Роман

Задание 1

Что такое балансировка нагрузки и зачем она нужна?

Приведите ответ в свободной форме.

Балансировка нагрузки - это распределение нагрузки на группу серверов, ее применени позводяет добиться повышения масштабируемости и отказоусточивости. 

Задание 2

Чем отличаются алгоритмы балансировки Round Robin и Weighted Round Robin? В каких случаях каждый из них лучше применять?

Приведите ответ в свободной форме.

Алгоритм Round Robin распределяет поступающие запросы по очереди для всех серверов. Является самым простым, обычно применяется когда поступаюие запросы примерно равнозначны по сложности и все сервера равноценны по производительности.

Алгоритм Weighted Round Robin аналогичен алгоритму Round Robin, но учитывает производительность сервера (согласно "весу" сервера в настроках балансировщика, динамически оценивать производительность не умеет), на сервера большей мощности отправляя соответственно больше запросов.

Задание 3

Установите и запустите Haproxy.

Приведите скриншот systemctl status haproxy, где будет видно, что Haproxy запущен.

![105-01](https://github.com/LipinRoman/10.5/blob/main/img/105-01.png)

Задание 4

Установите и запустите Nginx.

Приведите скриншот systemctl status nginx, где будет видно, что Nginx запущен.

![105-02](https://github.com/LipinRoman/10.5/blob/main/img/105-02.png)

Задание 5

Настройте Nginx на виртуальной машине таким образом, чтобы при запросе:

curl http://localhost:8088/ping

он возвращал в ответе строчку:

"nginx is configured correctly".

Приведите конфигурации настроенного Nginx сервиса и скриншот результата выполнения команды curl http://localhost:8088/ping.

``` /etc/nginx/nginx.conf
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
        worker_connections 768;
}

http {
        sendfile on;
        tcp_nopush on;
        tcp_nodelay on;
        keepalive_timeout 65;
        types_hash_max_size 2048;

        include /etc/nginx/mime.types;
        default_type application/octet-stream;

        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;

        gzip on;

        include /etc/nginx/conf.d/*.conf;
}

```

``` /etc/nginx/conf.d/default.conf
server {
        listen 80 default_server;
        listen [::]:80 default_server;

        root /var/www/html;

        index index.html index.htm index.nginx-debian.html;

        server_name _;

        location / {
                try_files $uri $uri/ =404;
        }

}

server {
        listen 8088;

        location /ping {
                return 200 "nginx is configured correctly \n";
        }

}
```

![105-03](https://github.com/LipinRoman/10.5/blob/main/img/105-03.png)

Задания со звёздочкой*

Эти задания дополнительные. Их выполнять не обязательно. На зачёт это не повлияет. Вы можете их выполнить, если хотите глубже разобраться в материале.

Задание 6*

Настройте Haproxy таким образом, чтобы при ответе на запрос:

curl http://localhost:8080/

он проксировал его в Nginx на порту 8088, который был настроен в задании 5 и возвращал от него ответ:

"nginx is configured correctly".

Приведите конфигурации настроенного Haproxy и скриншоты результата выполнения команды curl http://localhost:8080/.
