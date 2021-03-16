# NGINX UDP Load Balancing Example

```
user www-data;
worker_processes auto;
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;

events {
        worker_connections 1024;
        multi_accept on;
}

stream {
    upstream dns_servers {
        least_conn;
        server 192.168.100.1:1053 max_fails=1 fail_timeout=10s;
        server 192.168.100.1:2053 max_fails=1 fail_timeout=10s;
    }

    server {
        listen 53 udp reuseport;
        listen 53;
        proxy_pass dns_servers;
        proxy_responses 1;
        proxy_timeout 3s;
        proxy_connect_timeout 2s;
    }

}
```