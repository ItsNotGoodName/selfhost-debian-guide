# PHP Installation

Installs php7.3.

`/etc/nginx/conf.d/php-fpm.conf`
```
upstream php-fpm {
	server unix:/var/run/php/php7.3-fpm.sock;
}
```
## Disable apache2
```
systemctl disable apache2
```