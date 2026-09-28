# TP3 - Powershell
## Script 1
```powershell
<#------------------------------------------------------------------
Apprentissage PowerShell - Script n° 1
Fonction : Ce script cherche un fichier dans un dossier donné
Auteur CLB – 28/09/2026
--------------------------------------------------------------------#>
$cherche = $args[0]
$dossier = $args[1]
$nbfichier = 0
Write-Output "Recherche du fichier $cherche dans le dossier $dossier"
Get-ChildItem -Path $dossier -ErrorAction SilentlyContinue -Recurse | `
    Where-Object {$_.Name -eq $cherche} | `
    forEach-Object {
        Write-host ("le fichier $cherche est dans "+$_.DirectoryName)
        $nbfichier++
    }
Write-Host -foregroundcolor yellow "le fichier $cherche est présent dans $nbfichier dossiers" 
```
