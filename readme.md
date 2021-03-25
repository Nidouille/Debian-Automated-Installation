# **Introduction**

Mon fichier preceed pour générer une image de Debian 10 avec installation des dépôts pour Zabbix

# Création de l'image personnalisée

### Prérequis

Il nous faut les deux paquets suivant :

* xorriso : extraction de l'iso
* genisoimage : création de l'iso personnalisé

```
apt update && apt -y install xorriso genisoimage
```

### Download de l'image Debian

```
curl -LO# https://cdimage.debian.org/debian-cd/current/amd64/iso-cd/debian-10.8.0-amd64-netinst.iso
```

### Extraction de l'ISO

```
xorriso -osirrox on -indev debian-10.8.0-amd64-netinst.iso -extract / isofiles/
```

### Création de l'ISO personnalisé

#### Modification du boot loader

Cette modification permet de ne pas avoir le menu de sélection et de passer directement sur l'installation automatique

##### BIOS : isolinux

Modification du fichier isofiles/isolinux/isolinux.cfg en commentant ou enlevant la ligne `default vesamenu.c32`

##### UEFI : grub

Modification non faite, car je suis en vm

#### Ajout du fichier de pré configuration dans initrd

```
chmod +w -R isofiles/install.amd/
```

```
gunzip isofiles/install.amd/initrd.gz
```

```
echo preseed.cfg | cpio -H newc -o -A -F isofiles/install.amd/initrd gzip isofiles/install.amd/initrd
```

```
chmod -w -R isofiles/install.amd/
```

#### Génération du checksum MD5

```
cd isofiles/
```

```
chmod a+w md5sum.txt
```

```
md5sum `find -follow -type f` > md5sum.txt
```

```
chmod a-w md5sum.txt
```

```
cd ..
```

#### Création de l'ISO

```
chmod a+w isofiles/isolinux/isolinux.bin
```

```
genisoimage -r -J -b isolinux/isolinux.bin -c isolinux/boot.cat -no-emul-boot -boot-load-size 4 -boot-info-table -o debian-10-unattended.iso isofiles
```

 

 