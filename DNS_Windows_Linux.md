# Cohabitation DNS Windows et Linux
Le domaine Windows est bzh-boutique.intra
Le domaine Linux (web) est bzh-boutique.bzh
## 1. Configuration du DNS primaire sous Linux
Fichier /etc/bind/named.conf.options
```conf
options {
        directory "/var/cache/bind";

        validate-except {
                "bzh-boutique.intra";
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
```conf
//
// Do any local configuration here
//
zone "bzh-boutique.intra" {
        type forward;
        forward only;
        forwarders { 192.168.100.1; };
};

zone "bzh-boutique.bzh" {
        type master;
        file "db.bzh-boutique.bzh";
        //allow-transfer { 192.168.100.4; };
};

zone "100.168.192.in-addr.arpa" {
        type master;
        file "db.192.168.100.rev";
        //allow-transfer { 192.168.100.4; };
};
```
Fichier de zone /var/cache/bind/db.bzh-boutique.bzh
```conf
; Fichier de zone de bzh-boutique.bzh
$TTL 86400
@       IN      SOA     dns1.bzh-boutique.bzh. webmaster.bzh-boutique.bzh. (
                        2026101201      ; serial
                        86400           ; refresh
                        21600           ; retry
                        3600000         ; expire
                        3600 )          ; negative caching ttl
@       IN      NS      dns1.bzh-boutique.bzh.
@       IN      NS      dns2.bzh-boutique.bzh.

dns1    IN      A       192.168.100.3
dns2    IN      A       192.168.100.4
web1    IN      A       192.168.200.1
web2    IN      A       192.168.200.2
www     IN      A       192.168.200.3
```
