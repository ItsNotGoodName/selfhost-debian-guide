# TheLounge Installation
Download from <https://github.com/thelounge/thelounge/releases/tag/v4.1.0>
```
apt-get install curl software-properties-common
curl -sL https://deb.nodesource.com/setup_12.x | bash -
apt-get install nodejs
apt install ./thelounge.deb
systemctl start thelounge
systemctl enable thelounge
systemctl status thelounge
```
In `/etc/thelounge/config.js` set the variable to this `reverseProxy: true,`
## Nginx Configuration
`/etc/nginx/conf.d/thelounge.example.com.conf`
```
server {
    listen [::]:443 ssl;
    listen 443 ssl;

	server_name thelounge.example.com;

	location / {
		proxy_pass http://127.0.0.1:9000/;
		proxy_http_version 1.1;
		proxy_set_header Connection "upgrade";
		proxy_set_header Upgrade $http_upgrade;
		proxy_set_header X-Forwarded-For $remote_addr;
		proxy_set_header X-Forwarded-Proto $scheme;
	
		# by default nginx times out connections in one minute
		proxy_read_timeout 1d;
	}

}
```
## Active Directory Configuration
`/etc/thelounge/config.js`

```
ldap: {
        enable: true,
        url: "ldap://pdc.example.com",
        tlsOptions: {},
        primaryKey: "sAMAccountName",
        searchDN: {
                rootDN: "cn=viewer,ou=Server-Users,dc=example,dc=com",
                rootPassword: "password",
                filter: "(objectCategory=person)(memberOf:1.2.840.113556.1.4.1941:=CN=TheLounge Group,OU=Groups,dc=example,dc=com)(!(UserAccountControl:1.2.840.113556.1.4.803:=2))",
                base: "dc=example,dc=com",
                scope: "sub",
        },
},
```