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
$IPPasserelle = "172.16.0.254"
$DNSPrimaire = "172.16.0.1"
$DNSsecondaire = "1.1.1.1"
$DuréeDeBail = "14400"
$AdresseReseau = "172.16.0.0"

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

# Effacer l'étendue si elle existe déjà
if ((get-dhcpserverv4scope -ScopeId $AdresseReseau) -ne $null) {
    Remove-DhcpServerv4Scope -ScopeId $AdresseReseau -force
}

# Création d'une étendue
Add-DhcpServerv4Scope -Name $NomEtendue -StartRange $IPDebut -EndRange $IPFin -SubnetMask $masque
# Ajout des options de l'étendue
Set-DhcpServerv4OptionDefinition -OptionId 3 -DefaultValue $IPPasserelle
Set-DhcpServerv4OptionValue -OptionId 6 -ScopeId $AdresseReseau -Value $DNSPrimaire,$DNSsecondaire -Force
Set-DhcpServerv4OptionValue -OptionId 51 -ScopeId $AdresseReseau -Value $DuréeDeBail
# Activation de l'étendue
Set-DhcpServerv4Scope -ScopeId $AdresseReseau -Name $NomEtendue -State Active

# Vérification de l'étendue créée

Get-DhcpServerv4Scope -ScopeId $AdresseReseau
```
