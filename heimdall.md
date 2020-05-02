# Install Heimdall

## Requirements
```
apt install php sqlite php-mbstring php-xml php-sqlite3 php-common php-zip php-json
```


## Clone the repo

```
git clone https://github.com/linuxserver/Heimdall.git /opt/heimdall
```

## Set permissions

```
cd /opt
chown -R www-data:www-data /opt/heimdall/
chmod -R 755 /opt/heimdall/
```

## Create key for php
```
su - www-data -s /bin/bash
cd /opt/heimdall
php artisan key:generate
exit
```


## Nginx Configuration
Put this a place like the server block from nginx.md
```
root /opt/heimdall/public;
client_max_body_size 10M;
index  index.php index.html index.htm;

location / {
        try_files $uri $uri/ /index.php?$query_string;
}

location ~ \.php$ {
        fastcgi_pass    php-fpm;
        include         fastcgi_params;
        fastcgi_param   SCRIPT_FILENAME $document_root$fastcgi_script_name;
}
```
