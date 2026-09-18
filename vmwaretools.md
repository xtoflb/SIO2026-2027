# Installation et configuration des VMware tools sur une VM Linux
1. Installation des VMware tools sur la VM Linux
```bash
apt update
apt install -y open-vm-tools open-vm-tools-desktop fuse3
```   
2. Configuration du copier - coller sur l'ESXi
- Eteindre la VM
- Ouvrir les paramètres de la VM puis paramètres avancés.
- Ajoutez les attributs avec les valeurs ci-dessous :
```bash
isolation.tools.copy.disable = FALSE
isolation.tools.paste.disable = FALSE
isolation.tools.setGUIOptions.enable  = TRUE
```
- Redémarrer la VM
