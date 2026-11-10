# Configuration SNMP d'un commutateur Cisco :
```cisco
int vlan1
ip address 172.16.0.200 255.255.255.0
ip access-list standard SNMP_ACL
permit 172.16.0.5
snmp-server community sodecaf ro SNMP_ACL
snmp-server host 172.16.0.5 version 2c sodecaf
```
# Configuration SNMP d'un routeur Cisco :
```cisco
int G0/0/0
ip address 172.16.0.254 255.255.255.0
ip nat inside
no shut
int G0/0/1
ip address 172.17.2.232 255.255.0.0
ip nat outside
no shut
ip access-list standard SNMP_ACL
permit 172.16.0.5
snmp-server community sodecaf ro SNMP_ACL
snmp-server host 172.16.0.5 version 2c sodecaf
access-list 1 permit 172.16.0.0 255.255.255.0
ip nat inside source list 1 interface G0/0/1 overload
ip route 0.0.0.0 0.0.0.0 172.17.255.253
```
