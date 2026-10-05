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
