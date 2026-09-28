# Génération de la clé
```bash
corosync-keygen
```
# Modification du fichier /etc/corosync/corosync.conf
```
node {
                # Hostname of the node
                name: glpi1
                nodeid: 1
                ring0_addr: 172.16.0.61
                # Cluster membership node identifier
        }

        node {
                name: glpi2
                nodeid: 2
                ring0_addr: 172.16.0.62
        }
```

# Création des ressources
```bash
crm configure property stonith-enabled=false
crm configure property no-quorum-policy="ignore"
crm configure primitive IPFailover ocf:heartbeat:IPaddr2 params ip=172.16.0.60 cidr_netmask=24 nic=ens33 iflabel=VIP
crm resource move IPFailover glpi1
crm configure primitive serviceWeb lsb:apache2 op monitor interval=60s op start interval=0 timeout=60s op stop interval=0 timeout=60s
crm configure group servweb IPFailover serviceWeb meta migration-threshold="5"
```

# Création de la BDD et de l'utilisateur
```sql
create database glpi;
grant all privileges on glpi.* to 'glpi'@'localhost' identified by 'Btssio2017';
```
# Copie des copies sur glpi2
```bash
tar -czvf site.tar.gz glpi/
scp site.tar.gz etudiant@172.16.0.62:/home/etudiant/
mysqldump -u root -p --databases glpi > dump_glpi.sql
mysql -u root -p glpi < dump_glpi.sql
```

grant replication slave on *.* to 'replicateur'@'%' identified by 'Btssio2017';
show master status;
change master to master_host='172.16.0.61', master_user='replicateur', master_password='Btssio2017', master_log_file='mysql-bin.000001', master_log_pos=328;
start slave;
unlock tables;
