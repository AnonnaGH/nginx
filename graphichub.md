# Graphichub.org domain hosting
> Server IP: **209.188.21.162**

## Task need to be done
* Domain purchase from domain.mateors.com
* DNS A record value set from the domain control panel
* DNS Propagation check using https://dnschecker.org/#A/graphichub.org
* Virtual hosting  using nginx
* Website system service
* Firewall port add
* System service enable to start a service at boot

## Service setup
> nano /lib/systemd/system/graphichub.service

```
[Unit]
Description=graphichub website
After=network.target

[Service]
Type=simple
Restart=always
RestartSec=5s
WorkingDirectory=/home/mastererp/graphichub.org
ExecStart=/home/mastererp/graphichub.org/graphichub

[Install]
WantedBy=multi-user.target
```

## Obtaining a SSL Certificate
> sudo certbot --nginx -d graphichub.org -d www.graphichub.org


## Resources
*[DigitalOcean Tutorial](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-18-04)