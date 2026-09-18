# Mise en oeuvre de l'HTTPS sur un serveur web
# Utilisation d'une autorité de certification interne

## 1. Préparation de la machine CA
- Configuration IP : /etc/network/interfaces
```bash
allow-hotplug ens33
iface ens33 inet static
        address 172.16.0.20/24
        gateway 172.16.0.254
```
- Installation d'openssl
```bash
apt update && sudo apt upgrade -y
apt install openssl
```
## 2. configuration d'openssl
- Editez le fichier /etc/ssl/openssl.cnf
```bash
dir = /etc/ssl/sodecaf
certificate = $dir/certs/cacert.pem
```
- Création des dossiers et fichiers nécessaires
```bash
mkdir /etc/ssl/sodecaf
cd /etc/ssl/sodecaf/
mkdir certs
mkdir private
mkdir newcerts
touch index.txt
echo "01" > serial
```
## 3. Génération du certificat de l'autorité de certification
- Création de la clé privée de l'autorité de certification
```bash
openssl genrsa -des3 -out /etc/ssl/sodecaf/private/cakey.pem 4096
chmod 400 /etc/ssl/sodecaf/private/cakey.pem
```
- Création du certificat auto-signé de l'autorité de certification
```bash
cd /etc/ssl/sodecaf/
openssl req -new -x509 -days 1825 -key private/cakey.pem -out certs/cacert.pem
```
## 4. Génération du certificat du serveur web
On travaille sur le serveur web1
- Editez le fichier /etc/ssl/openssl.cnf
```bash
dir = /etc/ssl
```
- Création de la clé privée du serveur web
```bash
openssl genrsa -out /etc/ssl/private/srvwebkey.pem 4096
```
- Création du fichier de demande de certificat
```bash
openssl req -new -key private/srvwebkey.pem -out certs/srvwebkey_dem.pem
```
- Copie du fichier de demande de certificat sur la machine CA
```bash
scp srvwebkey_dem.pem etudiant@172.16.0.20:/home/etudiant/
```
On travaille sur le serveur CA
- Création du certificat
```bash
openssl ca -policy policy_anything -out /etc/ssl/sodecaf/certs/srvwebcert.pem -infiles /home/etudiant/srvwebkey_dem.pem 
```
- Copie du certificat sur le serveur web
```bash
scp /etc/ssl/sodecaf/certs/srvwebcert.pem etudiant@172.16.0.10:/home/etudiant
```
On travaille sur le serveur web srv-web1
- Déplacement et changement de propriétaire du certificat
```bash
mv /home/etudiant/srvwebcert.pem /etc/ssl/certs/
chown root:root /etc/ssl/certs/srvwebcert.pem
```
## 5. Configuration d'Apache2 sur le serveur web
- Activation du module ssl sous Apache2
```bash
a2enmod ssl
a2enmod rewrite
```
- Création du virtualhost pour https
```bash
cd /etc/apache2/sites-available/
cp sodecaf.conf sodecaf-ssl.conf
```
- Modification du fichier sodecaf-ssl.conf
```apache
<VirtualHost *:443>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/sodecaf
        DirectoryIndex sodecaf.html

        SSLEngine on
        SSLCertificateFile /etc/ssl/certs/srvwebcert.pem
        SSLCertificateKeyFile /etc/ssl/private/srvwebkey.pem

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
<VirtualHost *:80>
        RewriteEngine On
        RewriteCond %{HTTPS} !=on
        RewriteRule ^/?(.*) https://%{SERVER_NAME}/$1 [R,L]
</VirtualHost>
```
- Activation du site et redémarrage du service Apache2
```bash
a2ensite sodecaf-ssl.conf
systemctl restart apache2
```
## 6. Bonus : création d'un certificat autosigné, directement sur le serveur web
```bash
openssl genrsa -out /etc/ssl/private/srvwebkey.pem 4096
openssl req -new -x509 -days 1825 -key /etc/ssl/private/srvwebkey.pem -out /etc/ssl/certs/srvwebcert.pem
```
