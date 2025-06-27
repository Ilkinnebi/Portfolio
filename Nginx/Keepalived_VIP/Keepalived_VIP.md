sudo yum install nginx -y
sudo systemctl enable --now nginx 
yum install keepalived -y
systemctl enable --now keepalived

First server
vim /etc/keepalived/keepalived.conf
vrrp_instance VI_1 {
    state MASTER
    interface ens160
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.100.200
    }
}


second server
vim /etc/keepalived/keepalived.conf
vrrp_instance VI_1 {
    state BACKUP
    interface ens160
    virtual_router_id 51
    priority 90
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass 1111
    }
    virtual_ipaddress {
        192.168.100.200
    }
}

first server
cd /usr/share/nginx/html/
echo "This is First Server" >index.html
cd /etc/nginx/conf.d/
vim firstserver.conf


second server
cd /usr/share/nginx/html/
echo "This is second Server" >index.html
cd /etc/nginx/conf.d/
vim secondserver.conf





