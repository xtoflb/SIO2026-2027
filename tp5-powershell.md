# TP5 PowerShell et active Directory
## Script1 : création d'un compte utilisateur
```powershell
<#
fichier : TP5-script1-compteAD.ps1
Test de création de compte AD en powershell
#>

# Importer le module Active Directory
Import-Module ActiveDirectory

#New-ADOrganizationalUnit -Name "Employés" -Path "dc=sodecaf,dc=local"

New-ADUser -Name "Paul Bismuth" -GivenName Paul -Surname Bismuth `
  -SamAccountName pbismuth -UserPrincipalName pbismuth@sodecaf.local `
  -AccountPassword (Read-Host -AsSecureString "Mettez ici votre mot de passe") `
  -PassThru `
  -Path "ou=Employés,dc=sodecaf,dc=local" | Enable-ADAccount
```
## Script2 : import d'utilisateurs à partir d'un scv
```powershell
<#
fichier : TP5-script2-importUtilisateurs.ps1
Import d'utilisateurs à partir d'un fichier csv
pour la création de comptes AD
#>

# Importer le module Active Directory
Import-Module ActiveDirectory

# Création des UO
New-ADOrganizationalUnit -Name "Employés" -Path "dc=sodecaf,dc=local" -ProtectedFromAccidentalDeletion $false
New-ADOrganizationalUnit -Name "Accueil" -Path "ou=Employés,dc=sodecaf,dc=local" -ProtectedFromAccidentalDeletion $false
New-ADOrganizationalUnit -Name "Comptabilité" -Path "ou=Employés,dc=sodecaf,dc=local" -ProtectedFromAccidentalDeletion $false
New-ADOrganizationalUnit -Name "Informatique" -Path "ou=Employés,dc=sodecaf,dc=local" -ProtectedFromAccidentalDeletion $false

# Création des groupes



# Création des utilisateurs


```
