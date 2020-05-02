# Gitea Insallation
Do this first
<https://docs.gitea.io/en-us/install-from-binary/>
Then this
<https://docs.gitea.io/en-us/linux-service/>

## Create Database

Remember to change password.
```
mysql
CREATE DATABASE gitea;
CREATE USER 'giteauser'@'localhost';
GRANT ALL ON gitea.* TO 'giteauser'@'localhost' IDENTIFIED BY 'password' WITH GRANT OPTION;
flush privileges;
exit;
```

## Nginx Configuration
`/etc/nginx/conf.d/git.example.com.conf`
```
server {
   	listen [::]:443 ssl;
   	listen 443 ssl;

	client_max_body_size 10M;
	server_name git.example.com;

	location / {
		proxy_pass http://localhost:3000;
	}	
}
```
## Gitea Initial Configuration
Go to `https://git.example.com` and then create an account. It will prompt you to configure DB. Write the mariadb configs you made and also set SSH Server Domain to be `git.example.com` and Gitea Base URL `https://git.example.com`. Then click the bottom button and wait for it finish.

## Active Directory Configuration
Click on you profile > Site Administration > Authentication Source > Add Authentication Source
```
Host
    pdc.example.com
Port
    389
Bind DN
    viewer@example.com
Bind Password
    password
User Search Base
    dc=example,dc=com
User Filter
    (&(sAMAccountName=%s)(objectCategory=Person)(memberOf:1.2.840.113556.1.4.1941:=CN=Gitea Group,OU=Groups,DC=example,DC=com)(!(UserAccountControl:1.2.840.113556.1.4.803:=2)))
Admin Filter
    (memberOf:1.2.840.113556.1.4.1941:=CN=Gitea Admin Group,OU=Groups,DC=example,DC=com)
Username Attribute
    sAMAccountName
First Name Attribute
    givenName
Surname Attribute
    sn
Email Attribute
    userPrincipalName
Enable User Synchronization             -   CHECKED
This Authentication Source is Activated -   CHECKED
```