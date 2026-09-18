# Étendre disque Workstation → 40Go
VM arrêtée → Settings → Hard Disk → Utilities → Expand → 40GB
# Étendre /dev/sda1
## Après expand + boot
```bash
apt update && apt install -y cloud-guest-utils
```
## Rescan
```bash
echo 1 > /sys/block/sda/device/rescan
lsblk  # sda=40G, sda1=19G ?
```

## Étendre
```bash
growpart /dev/sda 1
resize2fs /dev/sda1
df -h
```
