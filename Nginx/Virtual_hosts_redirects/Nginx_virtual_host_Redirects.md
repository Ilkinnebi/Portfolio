sudo yum install nginx -y
sudo systemctl enable --now nginx 


cd /usr/share/nginx/html/
mkdir domen
cd domen/
mkdir domen1 domen2
echo "Welcome to www.firstsite.com" >/usr/share/nginx/html/domen/domen1/index.html
echo "Welcome to www.secondsite.com" >/usr/share/nginx/html/domen/domen2/index.html
cd /etc/nginx/conf.d
vim domen.conf
server {
    listen       80;
    server_name  www.firstsite.com;

location / {
        root   /usr/share/nginx/html/domen/domen1;
        index  index.html;
    }
}



server {
    listen       80;
    server_name  www.secondsite.com;

location / {
        root   /usr/share/nginx/html/domen/domen2;
        index  index.html;
    }
}


server {
    listen 80;
    server_name firstsite.com;
    return 301 http://www.firstsite.com$request_uri;
}


server {
    listen 80;
    server_name secondsite.com;
    return 301 http://www.secondsite.com$request_uri;
}


vim /etc/hosts
127.0.0.1 firstsite.com www.firstsite.com
127.0.0.1 www.firstsite.com firstsite.com

sudo nginx -t
sudo systemctl reload nginx

curl http://www.secondsite.com
Welcome to www.secondsite.com
curl -L http://secondsite.com
Welcome to www.secondsite.com

curl http://www.secondsite.com
Welcome to www.secondsite.com
curl -L http://secondsite.com
Welcome to www.secondsite.com



