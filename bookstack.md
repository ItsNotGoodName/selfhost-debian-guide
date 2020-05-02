# BookStack Installation

## Install packages.

```
apt install php php-cli php-zip php-json curl php-fpm php-curl php-mbstring php-ldap php-tidy php-xml php-zip php-gd php-mysql composer
```

## Create database
Remember to change password.
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

Configure .env to have the following mysql configuration.

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

## Nginx Configuration
`/etc/nginx/conf.d/bookstack.example.com.conf`
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
## Active Directory Configuration

I change `LDAP_REMOVE_FROM_GROUPS` to `true` and `LDAP_REMOVE_FROM_GROUPS` to `false` when I am done configuring. 

`/opt/bookstack/.env`
```
# General auth, Comment this out to go back to normal authentication
AUTH_METHOD=ldap

# The LDAP host, Adding a port is optional
LDAP_SERVER=pdc.example.com:389

# The base DN from where users will be searched within
LDAP_BASE_DN=dc=example,dc=com

# The full DN and password of the user used to search the server
# Can both be left as false to bind anonymously
LDAP_DN=viewer
LDAP_PASS=12345678

# A filter to use when searching for users
# The user-provided user-name used to replace any occurrences of '${user}'
LDAP_USER_FILTER= "(&(sAMAccountName=${user})(objectCategory=Person)(memberOf:1.2.840.113556.1.4.1941:=CN=BookStack Group,OU=Groups,DC=example,DC=com)(!(UserAccountControl:1.2.840.113556.1.4.803:=2)))"

# Set the default 'email' attribute. Defaults to 'mail'
LDAP_EMAIL_ATTRIBUTE=mail

# Set the property to use for a user's display name. Defaults to 'cn'
LDAP_DISPLAY_NAME_ATTRIBUTE=cn

LDAP_VERSION=3
LDAP_ID_ATTRIBUTE=BIN;objectGUID

# Enable LDAP group sync, Set to 'true' to enable.
LDAP_USER_TO_GROUPS=true

# LDAP user attribute containing groups, Defaults to 'memberOf'.
LDAP_GROUP_ATTRIBUTE="memberOf"

APP_DEBUG=true

# Remove users from roles that don't match LDAP groups.
LDAP_REMOVE_FROM_GROUPS=false
```