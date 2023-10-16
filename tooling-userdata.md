#!/bin/bash
mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-012fc475d2d77717b fs-067a8a0d0f498b79a:/ /var/www/
yum install -y httpd 
systemctl start httpd
systemctl enable httpd
yum module reset php -y
yum module enable php:remi-7.4 -y
yum install -y php php-common php-mbstring php-opcache php-intl php-xml php-gd php-curl php-mysqlnd php-fpm php-json
systemctl start php-fpm
systemctl enable php-fpm
git clone https://github.com/IwunzeGE/DevopsToolingWebsite.git
mkdir /var/www/html
cp -R /tooling-1/html/*  /var/www/html/
cd /tooling-1
mysql -h wakabetter.czpfy2twzuxf.us-west-1.rds.amazonaws.com -u admin -p wakabetter_db < tooling-db.sql
cd /var/www/html/
touch healthstatus
sed -i "s/$db = mysqli_connect('mysql.tooling.svc.cluster.local', 'admin', 'admin', 'tooling');/$db = mysqli_connect('wakabetter.czpfy2twzuxf.us-west-1.rds.amazonaws.com', 'admin', 'password', 'wakabetter_db');/g" functions.php
chcon -t httpd_sys_rw_content_t /var/www/html/ -R
systemctl restart httpd







