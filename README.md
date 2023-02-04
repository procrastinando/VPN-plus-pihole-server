# VPN + pihole server

## Add user and increase swap if neccesary

```
adduser docker
usermod -aG sudo docker
```
```
fallocate -l 1G /swapfile
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

## Install FTP server to copy vpn conf files if neccesary
```
apt update && apt upgrade -y
apt install vsftpd -y
mv /etc/vsftpd.conf /etc/vsftpd.conf_orig
nano /etc/vsftpd.conf
```

Copy the configurations, set local user directory or choose a custom:
* chroot_local_user=YES
* local_root=/media/jetson
```
listen=NO
listen_ipv6=YES
anonymous_enable=NO
local_enable=YES
write_enable=YES
local_umask=022
dirmessage_enable=YES
use_localtime=YES
xferlog_enable=YES
connect_from_port_20=YES
chroot_local_user=YES
secure_chroot_dir=/var/run/vsftpd/empty
pam_service_name=vsftpd
rsa_cert_file=/etc/ssl/certs/ssl-cert-snakeoil.pem
rsa_private_key_file=/etc/ssl/private/ssl-cert-snakeoil.key
ssl_enable=NO
pasv_enable=Yes
pasv_min_port=10000
pasv_max_port=10100
allow_writeable_chroot=YES
```
Restart the server and allow ports
```
systemctl restart vsftpd
ufw enable -y
ufw allow 20:22/tcp && ufw allow 10000:10100/tcp && ufw allow 1194/udp && ufw allow 51820/udp
```
> Open the neccesary ports in your server, 1194 for openvpn and 51820 for wireguard.

## Install Pihole
```
git clone --depth 1 https://github.com/pi-hole/pi-hole.git Pi-hole
cd "Pi-hole/automated install/"
bash basic-install.sh
```

> * If there is any error, edit `/etc/resolv.conf` and ser the dns: 8.8.8.8
> * Change the password `pihole -a -p`

## Install pivpn
```
curl -L https://install.pivpn.io | bash
```
> Run the command again and reconfigure it to install openvpn+wireguard server.

To generate a new conf:
* `pivpn ovpn -a nopass` for generate a openvpn conf without password
* `pivpn wg -a` for generate a wireguard conf
* `pivpn wg -qr` to show a qr code for scan
