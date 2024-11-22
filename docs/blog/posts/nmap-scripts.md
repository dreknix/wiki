---
date: 2024-11-22
categories:
  - Tools
  - UNIX
---

# Scripts for Nmap

[Nmap](https://nmap.org/) ("Network Mapper") is a utility for network discovery
and security auditing.

Besides basic network discovery and port scanning, Nmap has a builtin scripting
engine (NSE). With this scripting engine more complex network tasks can be
created. The Nmap installation comes with a wide variety of scripts
(`/usr/share/nmap/scripts`). The scripts can be applied for each host or service
found during network discovery, or before and after the scan.

<!-- more -->

The script execution can be configured with the following command line
parameters(see [nmap(1)](
https://manpages.debian.org/nmap.1.en.html){target="_blank"}):

* `--script`: comma separated list of directories, files or categories
* `--script-help`: show the help for the given scripts
* `--script-args`: provide additional parameters
* `--script-trace`: show all data send and received

In the following a few examples are given.

DHCP service discovery, sends a DHCP discovery broadcast and display the
received DHCP configuration parameters:

``` sh
sudo nmap \
         --script=broadcast-dns-service-discovery \
         --script-args='broadcast-dhcp-discover.mac=de:ad:be:ef:ca:fe' \
         -e eth0
```

Retrieve the SSH host keys of the target host:

``` sh
nmap --script=ssh-hostkey -p 22 login.example.org
```

Get information about the SSL certificate:

``` sh
nmap --script=ssl-cert -p 443 www.example.org
```

Print the HTTP headers:

``` sh
nmap --script=http-headers -p 80 www.example.org
```

Get list of RSS feeds from a website:

``` sh
nmap --script http-feed.nse -p www.example.org
```

Find possible subdomains:

``` sh
nmap --script=dns-brute --script-args dns-brute.domain=example.com
```

...
