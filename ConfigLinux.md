# Configuration de base d'une machine Linux
## 1. Configuration IP
Éditez le fichier /etc/network/interfaces
```bash
# This file describes the network interfaces available on your system
# and how to activate them. For more information, see interfaces(5).

source /etc/network/interfaces.d/*

# The loopback network interface
auto lo
iface lo inet loopback

# The primary network interface
allow-hotplug ens33
iface ens33 inet static
        address 172.16.0.10/24
        gateway 172.16.0.254
        dns-nameservers 1.1.1.1
```
## 2. Renommer la machine
Éditez le fichier /etc/hostname
```bash
srv-web1
```
Éditez le fichier /etc/hosts
```bash
127.0.0.1       localhost
127.0.1.1       srv-web1

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```
