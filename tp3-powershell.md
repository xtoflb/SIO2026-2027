# TP3 - Powershell
## Script 1
```powershell
<#------------------------------------------------------------------
Apprentissage PowerShell - Script n° 1
Fonction : Ce script cherche un fichier dans un dossier donné
Auteur BD – 17/12/2013
--------------------------------------------------------------------#>
$cherche = $args[0]
$dossier = $args[1]
Write-Output "Recherche du fichier $cherche dans le dossier $dossier"
Get-ChildItem -Path $dossier -ErrorAction SilentlyContinue -Recurse | `
    Where-Object {$_.Name -eq $cherche} | `
    forEach-Object {
        Write-Output ("le fichier $cherche est dans "+$_.DirectoryName)
    }
```
