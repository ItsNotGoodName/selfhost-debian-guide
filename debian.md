# Debian Installation

Go to <https://www.debian.org/distrib/netinst> and download by clicking on 'Small CDs or USB sticks'.

Boot to your iso and begin your install. Set software selection to SSH Server and standard system utilities.

After installation, login as root.

## Update and Install basic packages

```
apt update
apt update upgrade
apt install vim wget git
```

## Configure Path

```
echo "PATH=/usr/sbin:$PATH" >> ~/.profile
```

## SSH Configuration

Copy your ssh public key to the server.

```
ssh-copy-id root@'centos-ip-address-or-name'
```

#### Disable password authentication in `/etc/ssh/sshd_config`

```
ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no
PermitRootLogin prohibit-password
```

#### Restart sshd

```
systemctl restart sshd
```
