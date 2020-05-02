# Certbot Installation

Buy a domain name from a registrar, for example Namecheap. Move your dns server to CloudFlare since they are better for certbot.

## Download Certbot

```
mkdir -p /opt/certbot
cd /opt/certbot
wget https://dl.eff.org/certbot-auto
chmod a+x ./certbot-auto
./certbot-auto
```

## Obtain api key from Cloudflare

Go to your Cloudflare dashboard and click on my profile > API Tokens. The key needs `Zone:Zone:Read` & `Zone:DNS:Edit` permissions.

### Create secret folder
```
mkdir .secrets
cd .secrets
vim cloudflare.ini
```

### Put in your cloudflare api key

```
dns_cloudflare_api_token = TOKEN_HERE
```

### Set secure permission

```
chmod 600 cloudflare.ini
```

## Install Certbot DNS Plugin

```
cd /opt/eff.org/certbot/venv
source bin/activate
pip install certbot-dns-cloudflare
deactivate
```

## Create and Setup Wildcard Certificate

```
apt install nginx -y
cd /opt/certbot
DNS_NAME="example.com"
./certbot-auto -i nginx --server https://acme-v02.api.letsencrypt.org/directory --preferred-challenges dns -d "*.$DNS_NAME" -d "$DNS_NAME" --dns-cloudflare --dns-cloudflare-credentials .secrets/cloudflare.ini
```

### Test with dry run

```
./certbot-auto renew --dry-run
```

### Setup auto renewel in crontab.

```
0 0 * * * /opt/certbot/certbot-auto renew --post-hook "systemctl restart nginx"
```
