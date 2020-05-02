# Nginx Installation

Enable and start Nginx.
```
systemctl start nginx
systemctl enable nginx
```

For any future nginx subdomain's, put them in the `/etc/nginx/conf.d/` directory. The file name would be `subdomain.example.com.conf` or `example.com.conf`.

## Example `/etc/nginx/conf.d/example.com.conf`
```
server {
        listen [::]:443 ssl default_server; # managed by Certbot
        listen 443 ssl default_server; # managed by Certbot

        server_name  example.com;

        ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem; # managed by Certbot
        ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem; # managed by Certbot
        include /etc/letsencrypt/options-ssl-nginx.conf; # managed by Certbot
        ssl_dhparam /etc/letsencrypt/ssl-dhparams.pem; # managed by Certbot
}

server {
        listen 80;
        listen [::]:80;

        server_name example.com;

        if ($host = example.com) {
                return 301 https://$host$request_uri;
        } # managed by Certbot

        if ($host ~ ^[^.]+\.example\.com$) {
                return 301 https://$host$request_uri;
        } # managed by Certbot

        return 404; # managed by Certbot
}
```