# Rédiger une documentation Markdown avec VSCodium et la publier sur GitHub

Ce guide explique comment utiliser **VSCodium** pour rédiger des documents Markdown (`.md`), gérer leur historique avec **Git**, puis les publier dans un dépôt **GitHub**.

L’objectif est d’obtenir une chaîne simple et reproductible :

```text
VSCodium → Git local → GitHub
```

Vous rédigez vos supports localement, vérifiez les modifications, créez des commits, puis les synchronisez avec GitHub afin de les partager avec les étudiants.

---

## 1. Principes : qui fait quoi ?

| Outil | Rôle |
|---|---|
| **VSCodium** | Écrire et prévisualiser les fichiers Markdown ; modifier scripts, images et fichiers de configuration |
| **Git** | Gérer localement l’historique des versions, les branches et les différences entre versions |
| **GitHub** | Héberger le dépôt distant, partager les contenus, collaborer et conserver une copie distante |
| **PowerShell** | Exécuter les commandes Git sous Windows ; le terminal intégré de VSCodium peut aussi l’utiliser |

Un fichier Markdown est un fichier texte, généralement avec l’extension `.md`. Git conserve les versions successives de ces fichiers. GitHub héberge ensuite le dépôt Git et rend les documents consultables depuis un navigateur.

---

## 2. Prérequis

Avant de commencer, installez :

- [VSCodium](https://vscodium.com/)
- [Git for Windows](https://git-scm.com/download/win) sur Windows, ou le paquet `git` sous Linux
- Un compte GitHub
- Une connexion Internet pour les synchronisations avec GitHub

### 2.1 Vérifier Git

Ouvrez PowerShell et lancez :

```powershell
git --version
```

Vous devez obtenir une réponse similaire à :

```text
git version 2.x.x.windows.x
```

Si la commande n’est pas reconnue, installez Git puis fermez et rouvrez PowerShell et VSCodium.

### 2.2 Définir votre identité Git

Cette étape n’est à réaliser qu’une seule fois sur chaque poste :

```powershell
git config --global user.name "Prénom Nom"
git config --global user.email "votre-adresse@email.fr"
```

Vérifiez les valeurs enregistrées :

```powershell
git config --global --list
```

Utilisez de préférence l’adresse associée à votre compte GitHub si vous souhaitez que les commits apparaissent automatiquement dans votre profil GitHub.

---

## 3. Authentification vers GitHub avec SSH

L’authentification SSH évite de saisir régulièrement vos identifiants. Elle est recommandée sur votre poste personnel.

### 3.1 Créer une clé SSH

Dans PowerShell :

```powershell
ssh-keygen -t ed25519 -C "votre-adresse@email.fr"
```

- Appuyez sur `Entrée` pour accepter le chemin proposé, généralement `C:\Users\votre-compte\.ssh\id_ed25519`.
- Définissez une phrase de passe si le poste est personnel et protégé. Elle protège votre clé privée en cas de copie du fichier.

Affichez ensuite la clé **publique** :

```powershell
Get-Content "$HOME\.ssh\id_ed25519.pub"
```

Copiez entièrement la ligne affichée. Elle commence habituellement par `ssh-ed25519`.

> Ne partagez jamais le fichier privé `id_ed25519`. Seul le fichier `id_ed25519.pub` doit être copié dans GitHub.

### 3.2 Ajouter la clé à GitHub

1. Connectez-vous à GitHub.
2. Cliquez sur votre avatar, puis sur **Settings**.
3. Ouvrez **SSH and GPG keys**.
4. Cliquez sur **New SSH key**.
5. Indiquez un titre explicite, par exemple `PC-Windows-maison`.
6. Collez le contenu de `id_ed25519.pub` dans le champ **Key**.
7. Validez avec **Add SSH key**.

### 3.3 Tester la connexion

Dans PowerShell :

```powershell
ssh -T git@github.com
```

Au premier essai, confirmez l’empreinte du serveur uniquement si la connexion cible bien `github.com`. GitHub doit confirmer que l’authentification a réussi ; il ne fournit pas de shell distant.

---

## 4. Créer ou récupérer le dépôt local

Deux situations sont possibles : vous avez déjà un dépôt GitHub, ou vous partez d’un dossier local contenant déjà vos documents.

### 4.1 Situation A — Le dépôt GitHub existe déjà

C’est la méthode à privilégier quand le dépôt distant contient déjà des fichiers ou un historique.

1. Ouvrez GitHub et allez dans le dépôt voulu.
2. Cliquez sur **Code** puis copiez l’URL **SSH**, par exemple :

```text
git@github.com:VOTRE_COMPTE/documentation-bts-sio.git
```

3. Dans PowerShell, choisissez l’emplacement local dans lequel stocker vos dépôts :

```powershell
cd "$HOME\Documents"
```

4. Clonez le dépôt :

```powershell
git clone git@github.com:VOTRE_COMPTE/documentation-bts-sio.git
```

5. Ouvrez le dossier créé dans VSCodium :

```powershell
cd documentation-bts-sio
codium .
```

Si la commande `codium` n’est pas disponible, ouvrez VSCodium manuellement puis choisissez **File → Open Folder** et sélectionnez le dossier cloné.

### 4.2 Situation B — Le dossier local existe déjà

Utilisez cette procédure lorsque vous avez déjà, par exemple, un dossier `C:\Users\xtof\Documents\documentation-bts-sio` contenant des documents Markdown, mais sans dépôt Git.

#### Créer un dépôt vide dans GitHub

Dans GitHub :

1. Cliquez sur **New repository**.
2. Donnez un nom au dépôt, par exemple `documentation-bts-sio`.
3. Choisissez la visibilité :
   - **Public** : consultation possible par tous ; pratique pour des ressources ouvertes.
   - **Private** : accès uniquement aux personnes autorisées.
4. Pour un dossier local déjà existant, ne cochez pas la création automatique d’un README, d’un `.gitignore` ou d’une licence.
5. Créez le dépôt et copiez son URL SSH.

#### Initialiser puis relier le dossier

Dans PowerShell, adaptez les chemins et l’URL :

```powershell
cd "C:\Users\xtof\Documents\documentation-bts-sio"

git init -b main
git add .
git commit -m "Initialisation de la documentation"

git remote add origin git@github.com:VOTRE_COMPTE/documentation-bts-sio.git
git push -u origin main
```

Après le premier envoi, actualisez la page GitHub : vos fichiers doivent apparaître dans le dépôt.

> Si `git init -b main` génère une erreur parce que votre version de Git est ancienne, utilisez `git init`, puis `git branch -M main` avant le premier `git push`.

---

## 5. Ouvrir le dépôt dans VSCodium

Un dépôt Git correspond à un dossier local contenant un sous-dossier caché `.git`. Ouvrez toujours ce **dossier racine** dans VSCodium, pas un fichier Markdown isolé.

Dans VSCodium :

1. Cliquez sur **File → Open Folder**.
2. Sélectionnez le dossier racine du dépôt, par exemple `documentation-bts-sio`.
3. Cliquez sur **Select Folder**.
4. Si VSCodium demande si vous faites confiance au dossier, confirmez uniquement s’il s’agit de votre dépôt ou d’un dépôt fiable.

Vous devez voir dans la barre latérale :

- **Explorer** : les fichiers et dossiers ;
- **Source Control** : l’état Git et les modifications ;
- **Extensions** : les extensions utiles ;
- **Terminal** : accessible avec `` Ctrl+` ``.

---

## 6. Résolution des problèmes

Si git n'utilise pas le bon service ssh sur votre PC :

```powershell
git config --global core.sshCommand "C:/Windows/System32/OpenSSH/ssh.exe"
git config --global ssh.variant ssh
```

## 7. Structure conseillée du dépôt

Voici une organisation adaptée à des ressources BTS SIO/SISR :

```text
documentation-bts-sio/
├── README.md
├── 01-reseaux/
│   ├── vlan/
│   │   ├── cours-vlan.md
│   │   └── tp-vlan-trunk.md
│   ├── routage/
│   └── services-reseau/
├── 02-systemes/
│   ├── linux/
│   ├── windows-server/
│   └── samba-ad/
├── 03-cybersecurite/
│   ├── opnsense/
│   ├── wazuh/
│   └── analyse-logs/
├── 90-ressources/
│   ├── fiches-commandes/
│   └── glossaire.md
├── assets/
│   ├── images/
│   └── diagrammes/
├── .editorconfig
├── .gitignore
└── README.md
```

Conseils de nommage :

- Utilisez des noms explicites, en minuscules et avec des tirets : `tp-dns-bind9-debian.md`.
- Évitez espaces, accents et caractères spéciaux dans les noms de fichiers.
- Conservez une arborescence stable : les liens Markdown relatifs resteront alors valides.
- Stockez les captures et schémas dans `assets/images/` ou dans un dossier `assets/` situé près du document concerné.

---

## 8. Rédiger un document Markdown

Créez par exemple le fichier :

```text
01-reseaux/services-reseau/tp-dns-bind9-debian.md
```

Exemple de contenu :

```markdown
---
titre: "TP — Mettre en place un serveur DNS Bind9 sous Debian"
module: "Services réseau"
niveau: "BTS SIO SISR"
duree: "2 h"
tags:
  - dns
  - debian
  - bind9
  - sisr
---

# TP — Mettre en place un serveur DNS Bind9 sous Debian

## Objectifs

À l’issue du TP, vous saurez :

- Installer Bind9 sous Debian
- Configurer une zone directe
- Vérifier la résolution avec `dig`
- Identifier les erreurs courantes de configuration

## Prérequis

- Une machine Debian avec une adresse IP fixe
- Des droits `sudo`
- Une connectivité réseau fonctionnelle

## Installation

```bash
sudo apt update
sudo apt install -y bind9 bind9utils dnsutils
```

## Vérification

```bash
systemctl status bind9
ss -lntup | grep :53
```

## Test de résolution

```bash
dig @127.0.0.1 localhost
```

## Ressources

- [Documentation officielle Bind9](https://bind9.readthedocs.io/)
- [Retour à l’accueil](../../README.md)
```

### 8.1 Éléments Markdown courants

| Objectif | Syntaxe |
|---|---|
| Titre niveau 1 | `# Titre` |
| Titre niveau 2 | `## Titre` |
| Liste à puces | `- Élément` |
| Liste numérotée | `1. Étape` |
| Texte en code | `` `commande` `` |
| Bloc de code | <code>```bash<br>commande<br>```</code> |
| Lien | `[Libellé](chemin-ou-url)` |
| Image | `![Description](../../assets/images/schema.png)` |
| Citation | `> Information importante` |
| Tableau | `| Colonne A | Colonne B |` |

### 8.2 Liens internes : privilégier Markdown standard

Pour une consultation directe sur GitHub, utilisez des liens Markdown relatifs plutôt que des liens propres à Obsidian de type `[[Nom de note]]`.

Exemple :

```markdown
Consultez aussi la [fiche de diagnostic DNS](../../90-ressources/fiches-commandes/diagnostic-dns.md).
```

Avant de valider vos modifications, cliquez sur le lien dans l’aperçu Markdown ou vérifiez son chemin depuis GitHub.

---

## 9. Encodage et fins de ligne

Pour éviter les affichages incorrects de caractères accentués comme `ExÃ©cution`, enregistrez tous les documents et scripts en **UTF-8**.

Dans VSCodium :

1. Regardez l’encodage dans la barre d’état, en bas à droite.
2. Cliquez dessus si nécessaire.
3. Choisissez **Save with Encoding**.
4. Sélectionnez **UTF-8**.

Ajoutez un fichier `.editorconfig` à la racine du dépôt :

```ini
root = true

[*]
charset = utf-8
end_of_line = lf
insert_final_newline = true

[*.ps1]
charset = utf-8

[*.md]
trim_trailing_whitespace = true
```

L’utilisation de `LF` est pratique dans un dépôt qui contient aussi des scripts Bash, Docker Compose, Ansible ou des fichiers utilisés sous Debian. Git assure la traçabilité des fichiers ; les conventions de fin de ligne réduisent les différences inutiles entre Windows et Linux.

---

## 10. Exclure les fichiers inutiles ou sensibles

Créez un fichier `.gitignore` à la racine du dépôt pour empêcher l’ajout de fichiers temporaires, de secrets ou de données trop lourdes.

Exemple de base :

```gitignore
# Windows
Thumbs.db
Desktop.ini

# Fichiers temporaires et sauvegardes
*.tmp
*.bak
*.swp
*~

# Journaux
*.log

# Secrets et variables locales
.env
.env.*
*.key
*.pem
*.pfx
*.p12
id_rsa
id_ed25519

# Dossiers de build ou de cache
site/
.cache/

# Réglages locaux de VSCodium / VS Code
.vscode/settings.json
```

> Ne placez jamais dans un dépôt GitHub des mots de passe, jetons GitHub, clés privées SSH, fichiers `.env`, exports sensibles, certificats privés ou sauvegardes de machines virtuelles. Un fichier retiré après un commit peut rester accessible dans l’historique Git.

Si vous avez déjà ajouté un secret par erreur, considérez-le comme compromis : révoquez-le ou changez-le immédiatement, puis nettoyez l’historique avec une procédure dédiée.

---

## 11. Cycle de travail quotidien

### 11.1 Avant de modifier les documents

Lorsque vous reprenez un travail ou passez d’un poste à un autre, récupérez les modifications distantes :

```powershell
git pull
```

Puis contrôlez l’état du dépôt :

```powershell
git status
```

### 11.2 Rédiger et prévisualiser

1. Ouvrez ou créez votre fichier Markdown dans VSCodium.
2. Modifiez le contenu.
3. Lancez l’aperçu avec `Ctrl+Shift+V`.
4. Vérifiez titres, tableaux, blocs de code, liens et images.
5. Enregistrez avec `Ctrl+S`.

### 11.3 Examiner les modifications

Cliquez sur l’icône **Source Control** dans VSCodium ou utilisez le terminal :

```powershell
git status
git diff
```

- `git status` indique les fichiers créés, modifiés ou supprimés.
- `git diff` affiche les lignes réellement modifiées avant le commit.

### 11.4 Créer un commit et publier

Dans le terminal de VSCodium :

```powershell
git add .
git commit -m "Ajout du TP DNS Bind9"
git push
```

Adoptez des messages de commit courts et explicites :

```text
Ajout du TP DNS Bind9
Correction des liens de la fiche VLAN
Mise à jour des prérequis du TP OPNsense
Ajout d’un schéma de topologie réseau
```

Vous pouvez aussi utiliser l’interface **Source Control** de VSCodium :

1. Contrôlez les fichiers listés dans **Changes**.
2. Cliquez sur `+` pour indexer les fichiers souhaités, ou sur `+` de la section pour tout indexer.
3. Saisissez un message de commit.
4. Cliquez sur la validation du commit.
5. Utilisez **Sync Changes**, **Push** ou le menu `...` pour envoyer les commits vers GitHub.

Pour des modifications importantes, le terminal reste utile car il affiche explicitement chaque commande et chaque éventuel message d’erreur.

---

## 12. Travailler avec des branches

Une branche vous permet de préparer un nouveau TP ou une refonte sans modifier immédiatement la version publiée dans `main`.

Créer une branche :

```powershell
git switch -c ajout-tp-dns-bind9
```

Travaillez ensuite normalement :

```powershell
git add .
git commit -m "Ajout du TP DNS Bind9"
git push -u origin ajout-tp-dns-bind9
```

Sur GitHub, vous pourrez ouvrir une **pull request** pour relire les différences entre `ajout-tp-dns-bind9` et `main` avant fusion.

Lorsque le travail est finalisé :

```powershell
git switch main
git pull
```

Après fusion de la pull request sur GitHub :

```powershell
git pull
```

Puis, si la branche n’est plus utile localement :

```powershell
git branch -d ajout-tp-dns-bind9
```

Exemples de noms de branches :

```text
ajout-tp-dns-bind9
correction-liens-vlan
refonte-chapitre-wazuh
mise-a-jour-debian-13
```

---

## 13. Résoudre les situations courantes

### 13.1 `git push` est refusé

Si quelqu’un — ou vous-même depuis un autre PC — a ajouté des commits sur GitHub, récupérez-les d’abord :

```powershell
git pull --rebase
git push
```

En cas de conflit, Git indique les fichiers concernés. Ouvrez-les dans VSCodium, choisissez ou fusionnez les contenus, puis :

```powershell
git add NOM_DU_FICHIER
git rebase --continue
git push
```

N’utilisez pas `git push --force` sur une branche collaborative sans en comprendre les effets : cette commande peut réécrire l’historique distant et masquer le travail d’autres personnes.

### 13.2 Vous avez ajouté un fichier par erreur avant le commit

Pour le retirer de l’index sans le supprimer de votre disque :

```powershell
git restore --staged NOM_DU_FICHIER
```

Ajoutez ensuite une règle adaptée dans `.gitignore` si ce type de fichier ne doit jamais être publié.

### 13.3 Vous voulez annuler les modifications d’un fichier non commité

> Cette commande supprime les modifications locales du fichier concerné.

```powershell
git restore NOM_DU_FICHIER
```

Avant toute annulation, utilisez :

```powershell
git diff
```

### 13.4 Le dépôt distant n’est pas le bon

Affichez les remotes configurés :

```powershell
git remote -v
```

Modifiez l’URL de `origin` si besoin :

```powershell
git remote set-url origin git@github.com:VOTRE_COMPTE/NOM_DU_DEPOT.git
```

Puis vérifiez de nouveau :

```powershell
git remote -v
```

### 13.5 VSCodium ne détecte pas Git

Dans le terminal intégré, testez :

```powershell
git --version
```

Si la commande n’existe pas :

1. Installez Git.
2. Fermez complètement VSCodium.
3. Rouvrez VSCodium.
4. Ouvrez le dossier racine du dépôt, celui qui contient `.git`.

---

## 14. Commandes essentielles à retenir

| Commande | Effet |
|---|---|
| `git clone URL` | Télécharge un dépôt existant et son historique |
| `git status` | Affiche l’état des fichiers du dépôt |
| `git pull` | Récupère et intègre les modifications distantes |
| `git diff` | Affiche les modifications non encore indexées |
| `git add .` | Ajoute toutes les modifications au prochain commit |
| `git add fichier.md` | Ajoute un fichier précis au prochain commit |
| `git commit -m "message"` | Crée une version locale nommée |
| `git push` | Envoie les commits locaux vers GitHub |
| `git switch -c nom-branche` | Crée et active une nouvelle branche |
| `git switch main` | Revient sur la branche principale |
| `git log --oneline --decorate --graph -10` | Affiche les dix derniers commits sous forme compacte |
| `git remote -v` | Affiche les dépôts distants configurés |

---

## 15. Checklist avant publication

Avant chaque `git push`, contrôlez :

- [ ] Les fichiers Markdown sont enregistrés en UTF-8.
- [ ] Les liens internes et externes ont été vérifiés.
- [ ] Les images sont présentes dans le dépôt et utilisent des chemins relatifs valides.
- [ ] Les commandes sont placées dans des blocs de code avec le bon langage : `bash`, `powershell`, `yaml`, `json`.
- [ ] Aucun mot de passe, secret, jeton, clé privée ou fichier sensible n’est ajouté.
- [ ] `git diff` correspond bien aux changements voulus.
- [ ] Le message de commit décrit clairement la modification.
- [ ] `git push` s’est terminé sans erreur.
- [ ] Le rendu final est vérifié sur GitHub.

---

## 16. Évolution : publier un vrai site documentaire

GitHub affiche correctement les fichiers Markdown, mais une documentation volumineuse est plus agréable à consulter sous la forme d’un site avec menu, recherche et navigation hiérarchique.

Une évolution naturelle est :

```text
VSCodium
    ↓
Markdown versionné avec Git
    ↓
GitHub
    ↓
GitHub Actions
    ↓
MkDocs Material
    ↓
GitHub Pages
```

Vous gardez les mêmes fichiers Markdown. Une action GitHub peut générer automatiquement le site chaque fois que vous envoyez une modification sur la branche `main`.

Cette approche permet de distribuer les documents aux étudiants via une interface plus confortable, tout en conservant la source, l’historique et l’automatisation dans GitHub.

---

## 17. Résumé de la routine

Pour publier une modification depuis VSCodium :

```powershell
# Se placer dans le dossier du dépôt
cd "C:\Users\xtof\Documents\documentation-bts-sio"

# Récupérer les éventuelles modifications faites ailleurs
git pull

# Éditer et vérifier les fichiers dans VSCodium

# Examiner puis publier les modifications
git status
git diff
git add .
git commit -m "Description claire de la modification"
git push
```

Vous disposez ainsi d’un processus fiable : chaque version est datée, attribuée, réversible et synchronisée vers GitHub.
