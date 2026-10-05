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

# Import du fichier csv
$users = Import-Csv -Delimiter ";" -Path '.\utilisateurs sodecaf.csv'

# Création des UO
<#
New-ADOrganizationalUnit -Name "Employés" -Path "dc=sodecaf,dc=local" -ProtectedFromAccidentalDeletion $false
New-ADOrganizationalUnit -Name "Accueil" -Path "ou=Employés,dc=sodecaf,dc=local" -ProtectedFromAccidentalDeletion $false
New-ADOrganizationalUnit -Name "Comptabilité" -Path "ou=Employés,dc=sodecaf,dc=local" -ProtectedFromAccidentalDeletion $false
New-ADOrganizationalUnit -Name "Informatique" -Path "ou=Employés,dc=sodecaf,dc=local" -ProtectedFromAccidentalDeletion $false
#>

# Création des groupes



# Création des utilisateurs
foreach ($user in $users) {
    $nom = $user.lastname
    $prenom = $user.firstname
    $email = $user.$email
    $login = $prenom.substring(0,1)+$nom
    $login = $login.tolower()
    $password = "Btssio2017"
    $service = $user.Function

    Write-Output "$prenom $nom $login $password"

   switch ($service) {
    "ACCUEIL" { $OU="ou=Accueil,ou=Employés,dc=sodecaf,dc=local" }
    "INFORMATIQUE" { $OU="ou=Informatique,ou=Employés,dc=sodecaf,dc=local" }
    "COMPTABLE" { $OU="ou=Comptabilité,ou=Employés,dc=sodecaf,dc=local" }
    Default { $OU="ou=Employés,dc=sodecaf,dc=local" }
   }

   New-ADUser -Name "$prenom $nom" -GivenName $prenom -Surname $nom `
  -SamAccountName $login -UserPrincipalName $email `
  -AccountPassword (ConvertTo-SecureString $password -AsPlainText -Force) `
  -PassThru `
  -Path $OU | Enable-ADAccount

}

```
