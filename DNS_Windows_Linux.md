# Cohabitation DNS Windows et Linux
## 1. Configuration du DNS primaire sous Linux
Fichier /etc/bind/named.conf.options
```conf
options {
        directory "/var/cache/bind";

        validate-except {
                "bzh-boutique.bzh";
        };

        recursion yes;
         allow-query     { localhost; 192.168.10.0/24; 192.168.20.0/24; 192.168.30.0/24; 192.168.100.0/24; 192.168.200.0/24; };
         allow-recursion { localhost; 192.168.10.0/24; 192.168.20.0/24; 192.168.30.0/24; 192.168.100.0/24; 192.168.200.0/24; };


        forwarders {
                1.1.1.1;
        };
};
```
Fichier /etc/bind/named.conf.local

Fichier de zone /var/cache/bind/db.bzh-boutique.bzh

