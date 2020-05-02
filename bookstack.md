# BookStack Installation

## Install packages.

```
apt install php php-cli php-zip php-json curl php-fpm php-curl php-mbstring php-ldap php-tidy php-xml php-zip php-gd php-mysql composer
```

## Create database

```
mysql
CREATE DATABASE bookstack;
CREATE USER 'bookstackuser'@'localhost';
GRANT ALL ON bookstack.* TO 'bookstackuser'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
flush privileges;
exit;
```

## Download BookStack

```
cd /opt/
git clone https://github.com/BookStackApp/BookStack.git --branch release --single-branch bookstack
cd bookstack
cp .env.example .env
vim .env
```

Configure .env to have mysql configuration

```
# Database details
DB_HOST=localhost
DB_DATABASE=bookstack
DB_USERNAME=bookstackuser
DB_PASSWORD=password
```

## Set permisions
```
cd /opt
chown -R www-data:www-data bookstack
chmod -R 755 bookstack
chmod 600 bookstack/.env
```

### Compose

```
su - www-data -s /bin/bash
cd /opt/bookstack
composer install --no-dev

php artisan key:generate
# YES
php artisan migrate
# YES

exit
```

## Nginx `/etc/nginx/conf.d/bookstack.example.com.conf`
```
server {
        listen [::]:443 ssl;
        listen 443 ssl;
        root /opt/bookstack/public;

        server_name bookstack.example.com;

        client_max_body_size 100M;
        index  index.php index.html index.htm;

        location / {
                try_files $uri $uri/ /index.php?$query_string;
        }

        location ~ \.php$ {
                include snippets/fastcgi-php.conf;
                fastcgi_pass     php-fpm;
                fastcgi_param   SCRIPT_FILENAME $document_root$fastcgi_script_name;
         }
}
```