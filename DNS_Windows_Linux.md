# Cohabitation DNS Windows et Linux
## Introduction
Dans le projet BZH-boutique, les serveurs DNS Linux et Windows remplissent des rôles complémentaires. L’objectif est de permettre aux postes clients Linux et Windows d’utiliser les serveurs BIND comme DNS dans leur configuration IP, tout en permettant aux postes Windows de rejoindre le domaine Active Directory.
Ce document utilise les noms de domaines suivants :
- `bzh-boutique.intra` : domaine Active Directory, dont la zone DNS est hébergée par les serveurs Windows.
- `bzh-boutique.bzh` : zone DNS des services web, administrée sur les serveurs Linux BIND.

## Répartition des rôles
| Serveur | Adresse IP | Rôle dans la configuration présentée |
|---|---|---|
| DNS Windows / contrôleur de domaine | `192.168.100.1` | Hébergement de la zone `bzh-boutique.intra` et des enregistrements nécessaires à Active Directory |
| BIND `dns1` | `192.168.100.3` | DNS utilisé par les clients, primaire de la zone web et transmission des requêtes AD |
| BIND `dns2` | `192.168.100.4` | Secondaire prévu pour la zone web ; configuration de transmission AD à ajouter également |

```text
Postes clients Linux et Windows
          |
          | DNS configurés : dns1 et dns2
          v
      Serveurs BIND
          |
          +-- bzh-boutique.bzh   --> Zone locale des services web
          |
          +-- bzh-boutique.intra --> DNS Windows / Active Directory
          |
          +-- Autres domaines   --> Redirecteur DNS général
```

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
