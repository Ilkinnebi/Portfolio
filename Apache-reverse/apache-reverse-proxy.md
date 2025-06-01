sudo yum install httpd -y
sudo systemctl enable cd /var/www

mkdir domain1 domain2
echo "Its domain1" > domain1/index.html
echo "Its domain2" > domain2/index.html

cd /etc/httpd/conf.d
vim domain.conf

<VirtualHost *:80>
    ServerName domain1
    ServerAdmin murad@domain1
    DocumentRoot /var/www/domain1
</VirtualHost>

<VirtualHost *:80>
    ServerName domain2
    ServerAdmin murad@domain2
    DocumentRoot /var/www/domain2
</VirtualHost>

curl -H "Host: domain1" http://ip
curl -H "Host: domain2" http://ip

Its domain1
Its domain2

sudo yum install mod_proxy mod_proxy_http -y

cd /etc/httpd/conf.d
vim proxy.conf

<VirtualHost *:80>
    ServerName proxytest
    ProxyRequests Off
    ProxyPreserveHost On

    ProxyPass /app http://localhost:8080/
    ProxyPassReverse /app http://localhost:8080/
</VirtualHost>

sudo apachectl configtest
sudo systemctl restart httpd
