# TP4 - PowerShell
## Script tp4-dhcp.ps1
```powershell
<#------------------------------------------------------------------
Apprentissage PowerShell - Script tp4-DHCP.ps1
Fonction : installation et configuration du service DHCP
Auteur CLB – 29/09/2026
--------------------------------------------------------------------#>

$NomServeur = "srv-win-core1.sodecaf.local"
$AdresseServeur = "172.16.0.1"
$NomEtendue = "DHCP_Sodecaf"
$IPDebut = "172.16.0.150"
$IPFin = "172.16.0.200"
$masque = "255.255.255.0"

# Installation de la fonctionnalité DHCP sur le serveur
Install-WindowsFeature -Name DHCP -IncludeManagementTools
# Création d'un groupe de sécurité DHCP
Add-DhcpServerSecurityGroup
# Redémarrage du service DHCP
Restart-Service dhcpserver
# Autoriser le serveur DHCP dans l'annuaire
Add-DhcpServerInDC -DnsName $NomServeur -IPAddress $AdresseServeur
# Post-déploiement du service DHCP
Set-ItemProperty –Path registry::HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\ServerManager\Roles\12 –Name ConfigurationState –Value 2

# Création d'une étendue
Add-DhcpServerv4Scope -Name $NomEtendue -StartRange $IPDebut -EndRange $IPFin -SubnetMask $masque
```
