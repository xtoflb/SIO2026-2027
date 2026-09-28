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
# Copie des fichiers sur glpi2 (BDD et web)
## Sur glpi1
```bash
tar -czvf site.tar.gz /var/www/glpi/
scp site.tar.gz etudiant@172.16.0.62:/home/etudiant/
mysqldump -u root -p --databases glpi > dump_glpi.sql
scp dump_glpi.sql etudiant@172.16.0.62:/home/etudiant/
```
## Sur glpi2
```sql
create database glpi;
grant all privileges on glpi.* to 'glpi'@'localhost' identified by 'Btssio2017';
```
```bash
tar -xvf site.tar.gz
mysql -u root -p glpi < dump_glpi.sql
```
# Mise en place de la réplication
## Sur glpi1
Création du dossier /var/log/mysql
```bash
mkdir /var/log/mysql
chmod 777 /var/log/mysql
```
Modification de la configuration SQL dans /etc/mysql/mariadb.conf.d/50-server.cnf
```
#bind-address = 127.0.0.1
log_error = /var/log/mysql/error.log
server-id              = 1
log_bin                = /var/log/mysql/mysql-bin.log
expire_logs_days        = 10
max_binlog_size        = 100M
binlog_do_db    = glpi
```
Création d'un compte de réplication sur glpi1
```sql
grant replication slave on *.* to 'replicateur'@'%' identified by 'Btssio2017';
show master status;
```
## Sur glpi2
Modification de la configuration SQL dans /etc/mysql/mariadb.conf.d/50-server.cnf
```
server-id              = 2
expire_logs_days        = 10
max_binlog_size        = 100M
master-retry-count  = 20
replicate-do-db = glpi
```
```sql
change master to master_host='172.16.0.61', master_user='replicateur', master_password='Btssio2017', master_log_file='mysql-bin.000001', master_log_pos=328;
start slave;
```
