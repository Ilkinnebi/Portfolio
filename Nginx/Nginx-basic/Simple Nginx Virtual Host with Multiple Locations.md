Simple Nginx Virtual Host with Multiple Locations

Install and start Nginx

sudo yum install -y nginx
sudo systemctl enable --now nginx


sudo mkdir -p /usr/share/nginx/html/test.az/main
sudo mkdir -p /usr/share/nginx/html/test.az/first1
sudo mkdir -p /usr/share/nginx/html/test.az/second2


echo "Its MAIN PAGE!" | sudo tee /usr/share/nginx/html/test.az/main/index.html
echo "1ci sehife" | sudo tee /usr/share/nginx/html/test.az/first1/index.html
echo "2ci sehife" | sudo tee /usr/share/nginx/html/test.az/second2/index.html


sudo tee /etc/nginx/conf.d/test.az.conf > /dev/null <<EOF
server {
    listen 80;
    server_name test.az;

    location / {
        root /usr/share/nginx/html/test.az/main;
        index index.html;
    }

    location = /first {
        return 301 http://test.az/first/;
    }

    location /first/ {
        alias /usr/share/nginx/html/test.az/first1/;
        index index.html;
    }

    location = /second {
        return 301 http://test.az/second/;
    }

    location /second/ {
        alias /usr/share/nginx/html/test.az/second2/;
        index index.html;
    }

    error_page 404 /404.html;
    location = /404.html {
        root /usr/share/nginx/html/test.az/main;
        internal;
    }
}
EOF


echo "<h1>404 Not Found</h1><p>Sorry, the page you requested was not found.</p>" | sudo tee /usr/share/nginx/html/test.az/main/404.html


sudo nginx -t
sudo systemctl reload nginx


curl http://test.az/
curl http://test.az/first/
curl http://test.az/second/
curl -I http://test.az/first
curl -I http://test.az/second


vim /etc/hosts
192.168.x.x test.az

C:\Windows\System32\drivers\etc\hosts
192.168.x.x test.az
