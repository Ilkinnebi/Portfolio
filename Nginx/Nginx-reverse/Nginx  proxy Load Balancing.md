sudo yum install nginx -y
sudo systemctl enable --now nginx 


cd /usr/share/nginx/html/
mkdir backend
cd backend/
mkdir backend1 backend2
echo "it is my first server" >backend1/index.html
echo "it is my second server" >backend2/index.html
cd /etc/nginx/conf.d
vim serven.conf
server {
    listen       8080;
    server_name  domain1;

location / {
        root   /usr/share/nginx/html/backend/backend1;
        index  index.html;
    }

}



server {
    listen       8081;
    server_name  domain2;

location / {
        root   /usr/share/nginx/html/backend/backend2;
        index  index.html;
    }

}


cd /etc/nginx/conf.d/
vim proxy.conf


upstream backend {
    server 127.0.0.1:8080;
    server 127.0.0.1:8081;
}


server {
    listen 80;
    server_name _;

    location / {
        proxy_set_header Host $host;
        proxy_pass http://backend;
    }
}



vim /etc/hosts
127.0.0.1 proxy

C:\Windows\System32\drivers\etc\hosts
127.0.0.1 proxy

vim ../nginx.conf

# server {
#     listen       80; #default_server;
#     listen       [::]:80; #default_server;
#     server_name  _;
#     root         /usr/share/nginx/html;
#
#     # Load configuration files for the default server block.
#     include /etc/nginx/default.d/*.conf;
#
#     location / {
#     }
#
#     error_page 404 /404.html;
#         location = /40x.html {
#     }
#
#     error_page 500 502 503 504 /50x.html;
#         location = /50x.html {
#     }
# }


sudo nginx -t
sudo systemctl reload nginx

curl http://127.0.0.1/
it is my first server
curl http://127.0.0.1/
it is my second server

