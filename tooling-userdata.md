#!/bin/bash
mkdir /var/www/
sudo mount -t efs -o tls,accesspoint=fsap-04f632ea0aa4be975 fs-09a33a8538674f03b:/ /var/www/
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
mysql -h zhikbee-rds.clvschp3b4qk.us-east-2.rds.amazonaws.com -u admin -p toolingdb_db < tooling-db.sql
cd /var/www/html/
touch healthstatus
sed -i "s/$db = mysqli_connect('mysql.tooling.svc.cluster.local', 'admin', 'admin', 'tooling');/$db = mysqli_connect('zhikbee-rds.clvschp3b4qk.us-east-2.rds.amazonaws.com', 'admin', 'toolingdb', 'wakabetter_db');/g" functions.php
chcon -t httpd_sys_rw_content_t /var/www/html/ -R
systemctl restart httpd







