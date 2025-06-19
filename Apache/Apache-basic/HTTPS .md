1. yum install httpd

2.vim /etc/httpd/conf.d ilkin.conf

<VirtualHost *:80>
    ServerName ilkin.com
    ServerAdmin ilkin@ilkin.com
    DocumentRoot /ilkin.com
</VirtualHost>

3. vim /etc/httpd/conf/httpd.conf

<Directory /ilkin.com>
  Require all granted
  AllowOverride None
</Directory>

4. mkdir /ilkin.com

5. semange fcontext --list | grep httpd

6. semanage fcontext -a -t httpd_sys_content_t "/ilkin.com(/.*)?"

7. restorecon -R -v /ilkin.com/

8. mkdir 1page   echo "Its 1ST PAGE!" >index.html
   mkdir 2page   echo "Its 2ST PAGE!" >index.html
   echo "Its MAIN PAGE!" >index.html

9. 
<VirtualHost *:80>
    ServerName ilkin.com
    ServerAdmin ilkin@ilkin.com
    DocumentRoot /ilkin.com

    Alias /first    /ilkin.com/1page
    Alias /second   /ilkin.com/2page

</VirtualHost>

 