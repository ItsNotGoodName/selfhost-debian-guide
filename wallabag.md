# Wallabag Installation
## Install Packages
```
apt install php-ctype php-dom php-simplexml php-json php-gd php-mbstring php-xml php-tidy php-curl php-gettext php-tokenizer php-bcmath php-intl php-fpm php-mysql 
```
## Create Database
```
mysql
CREATE DATABASE wallabag;
CREATE USER 'wallabaguser'@'localhost';
GRANT ALL ON wallabag.* TO 'wallabaguser'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
flush privileges;
exit;
```
## Clone
```
cd /opt
git clone https://github.com/wallabag/wallabag.git
```
## Set permisions
```
cd /opt
chown -R www-data:www-data wallabag
chmod -R 750 wallabag
```
## Install

```
su - www-data -s /bin/bash
cd /opt/wallabag
make install

# It will ask for addition options

exit
```
## Nginx Configuration
`/etc/nginx/conf.d/wallabag.example.com.conf`

```
server {
        listen [::]:443 ssl;
        listen 443 ssl;

        root /opt/wallabag/web;
        server_name wallabag.example.com;
        location / {
            try_files $uri /app.php$is_args$args;
        }
        location ~ ^/app\.php(/|$) {
            fastcgi_pass php-fpm;
            fastcgi_split_path_info ^(.+\.php)(/.*)$;
            include fastcgi_params;
            fastcgi_param  SCRIPT_FILENAME  $realpath_root$fastcgi_script_name;
            fastcgi_param DOCUMENT_ROOT $realpath_root;
            internal;
        }

        location ~ \.php$ {
            return 404;
        }

        error_log /var/log/nginx/wallabag_error.log;
        access_log /var/log/nginx/wallabag_access.log;
}
```