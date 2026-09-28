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
## Script 2
```powershell
<#-------------------------------------------------------------------
Apprentissage PowerShell - Script n° 2
Auteur CLB – 28/09/2026
---------------------------------------------------------------------#>
$dossier = $args[0]
Write-Host "calcul en cours sur $dossier"
Get-ChildItem -Path $dossier -Recurse -Force -ErrorAction SilentlyContinue | `
    Where-Object {$_PsisContainer -ne 0} | `
    Where-Object {$_.Length -gt 1MB} | `
    Measure-Object -property Length -Sum | `
        ForEach-Object {
        $total = $_.sum / 1MB
        write-host -foregroundColor yellow ("le dossier "+$dossier+" contient {0:#,##0.0} MB" -f $total)
 }```
## Script 3
```powershell
<#-------------------------------------------------------------------
Apprentissage PowerShell - Script n° 3
Auteur CLB – 28/09/2026
---------------------------------------------------------------------#>
$invite = "saisissez une couleur"
$couleur = Read-Host $invite
Write-Host -ForegroundColor $couleur ("vous avez demandé à écrire en "+$couleur)
```
