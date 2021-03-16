# NGINX Reverse Proxy Sample VHOST for PiHole DNS
```
server {

        server_name howard.dbordones.com;

        listen 80;
        listen [::]:80;

        access_log /var/log/nginx/reverse-access.log;
        error_log /var/log/nginx/reverse-error.log;

        client_max_body_size 100M;
        proxy_read_timeout 600s;
        proxy_buffers 16 4k;
        proxy_buffer_size 2k;

        location /ph1/ {
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_pass http://192.168.100.1:1080/admin/;
                proxy_read_timeout 90;
        }

        location /ph2/ {
                proxy_set_header Host $host;
                proxy_set_header X-Real-IP $remote_addr;
                proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
                proxy_pass http://192.168.100.1:2080/admin/;
                proxy_read_timeout 90;
        }

}
```