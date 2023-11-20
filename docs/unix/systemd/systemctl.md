# systemctl

* [systemctl(1)](https://manpages.debian.org/systemctl.1.en.html){target="_blank"}

## State of systemd and services

Overview about all services:

``` console
systemctl
```

Get information about a service:

``` console
systemctl status cron.service
```

## Content of service, timer, etc

``` console
systemctl cat cron.service
```

## Boot into BIOS Setup

``` console
sudo systemctl reboot --firmware-setup
```
