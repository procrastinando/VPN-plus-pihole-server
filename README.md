# VPN + pihole server

## Add user and increase swap if neccesary

```
adduser docker
usermod -aG sudo docker
```

fallocate -l 1G /swapfile 
chmod 600 /swapfile 
mkswap /swapfile 
swapon /swapfile 
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab

