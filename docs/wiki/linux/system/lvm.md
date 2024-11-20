---
title: WIKI - Linux - Généralités système - LVM
icon: material/disk
tags:
    - Wiki
    - Linux
    - LVM
---

# LVM sous Linux

[Référence : https://www.linuxtricks.fr/wiki/lvm-sous-linux-volumes-logiques](https://www.linuxtricks.fr/wiki/lvm-sous-linux-volumes-logiques)

## Cas pratique #01

### Création et montage d'un volume logigue avec point de montage sous `/home`


Liste et  caractéristiques des disques avant configuration :

```bash
fdisk -l
```

```bash
Disque /dev/sdb : 160 GiB, 171798691840 octets, 335544320 secteurs
Modèle de disque : QEMU HARDDISK
Unités : secteur de 1 × 512 = 512 octets
Taille de secteur (logique / physique) : 512 octets / 512 octets
taille d E/S (minimale / optimale) : 512 octets / 512 octets


Disque /dev/sdc : 32 GiB, 34359738368 octets, 67108864 secteurs
Modèle de disque : QEMU HARDDISK
Unités : secteur de 1 × 512 = 512 octets
Taille de secteur (logique / physique) : 512 octets / 512 octets
taille d E/S (minimale / optimale) : 512 octets / 512 octets


Disque /dev/sda : 25 GiB, 26843545600 octets, 52428800 secteurs
Modèle de disque : QEMU HARDDISK
Unités : secteur de 1 × 512 = 512 octets
Taille de secteur (logique / physique) : 512 octets / 512 octets
taille d E/S (minimale / optimale) : 512 octets / 512 octets
Type d étiquette de disque : dos
Identifiant de disque : 0xcd15c49b

Périphérique Amorçage   Début      Fin Secteurs Taille Id Type
/dev/sda1    *           2048   999423   997376   487M 83 Linux
/dev/sda2             1001470 52426751 51425282  24,5G  5 Étendue
/dev/sda5             1001472 52426751 51425280  24,5G 8e LVM Linux


Disque /dev/mapper/SRVIGEOBD25--vg-root : 23,54 GiB, 25274875904 octets, 49364992 secteurs
Unités : secteur de 1 × 512 = 512 octets
Taille de secteur (logique / physique) : 512 octets / 512 octets
taille d E/S (minimale / optimale) : 512 octets / 512 octets


Disque /dev/mapper/SRVIGEOBD25--vg-swap_1 : 976 MiB, 1023410176 octets, 1998848 secteurs
Unités : secteur de 1 × 512 = 512 octets
Taille de secteur (logique / physique) : 512 octets / 512 octets
taille d E/S (minimale / optimale) : 512 octets / 512 octets
```

#### Schéma de partitionnement

| Disque   | Taille  | PV        | VG                  | LV     | LV Path                          | Système de fichier | Montage |
| -------- | ------- | --------- | ------------------- | ------ | -------------------------------- | ------------------ | ------- |
| /dev/sda | 23,54GB | /dev/sda5 | SRVIGEOBD25-vg      | root   | /dev/SRVIGEOBD25-vg/root         | ext4               | /       |
| /dev/sda | 976MB   | /dev/sda5 | SRVIGEOBD25-vg      | swap_1 | /dev/SRVIGEOBD25-vg/swap_1       | swap               | swap    |
| /dev/sdc | 32GB    | /dev/sdc1 | SRVIGEOBD25-home-vg | home   | /dev/SRVIGEOBD25-home-vg/home-lv | ext4               | /home   |

#### Création du volume logique /home sur /dev/sdc

Prérequis LVM - Partitionnement du disque - en créant la partition et en définissant le type sur **LVM Linux** :
```bash
cfdisk /dev/sdc
```

Vérification de l'opération :
```bash
fdisk -l /dev/sdc
```

```bash
Disque /dev/sdc : 32 GiB, 34359738368 octets, 67108864 secteurs
Modèle de disque : QEMU HARDDISK
Unités : secteur de 1 × 512 = 512 octets
Taille de secteur (logique / physique) : 512 octets / 512 octets
taille d E/S (minimale / optimale) : 512 octets / 512 octets
Type d étiquette de disque : gpt
Identifiant de disque : 319DF41A-C31C-124A-B115-E44284196AB7

Périphérique Début      Fin Secteurs Taille Type
/dev/sdc1     2048 67106815 67104768    32G LVM Linux
```

#### Création du volume physique PV
```bash
pvcreate /dev/sdc1
Physical volume "/dev/sdc1" successfully created.
```

Afficher les infos du PV créé :
```bash
pvdisplay /dev/sdc
```

```bash
--- Physical volume ---
  PV Name               /dev/sda5
  VG Name               SRVIGEOBD25-vg
  PV Size               24,52 GiB / not usable 2,00 MiB
  Allocatable           yes
  PE Size               4,00 MiB
  Total PE              6277
  Free PE               7
  Allocated PE          6270
  PV UUID               NYyRyD-FZFJ-UBqS-fG94-7ZoG-CCMc-CGBRxY

  "/dev/sdc1" is a new physical volume of "<32,00 GiB"
  --- NEW Physical volume ---
  PV Name               /dev/sdc1
  VG Name
  PV Size               <32,00 GiB
  Allocatable           NO
  PE Size               0
  Total PE              0
  Free PE               0
  Allocated PE          0
  PV UUID               yvLOCy-Xenj-2Tqt-A2HI-MKfL-x0wG-XfpY6T
```

#### Création du groupe de volume VG

```bash
vgcreate SRVIGEOBD25-home-vg /dev/sdc1
Volume group "SRVIGEOBD25-home-vg" successfully created
```


Afficher les infos du VG créé :
```bash
vgdisplay
```

```bash
 --- Volume group ---
  VG Name               SRVIGEOBD25-home-vg
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  1
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                0
  Open LV               0
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <32,00 GiB
  PE Size               4,00 MiB
  Total PE              8191
  Alloc PE / Size       0 / 0
  Free  PE / Size       8191 / <32,00 GiB
  VG UUID               Ynkint-gq4S-mQ87-0hRs-StYU-33Y2-xjonMM

  --- Volume group ---
  VG Name               SRVIGEOBD25-vg
  System ID
  Format                lvm2
  Metadata Areas        1
  Metadata Sequence No  3
  VG Access             read/write
  VG Status             resizable
  MAX LV                0
  Cur LV                2
  Open LV               2
  Max PV                0
  Cur PV                1
  Act PV                1
  VG Size               <24,52 GiB
  PE Size               4,00 MiB
  Total PE              6277
  Alloc PE / Size       6270 / 24,49 GiB
  Free  PE / Size       7 / 28,00 MiB
  VG UUID               mQJ0KH-La6N-Qd7t-g2Gv-Bj5I-vWuj-xMdWyV
```


#### Création du volume logique LV

```bash
lvcreate -n home-lv -l 100%FREE SRVIGEOBD25-home-vg
Logical volume "home-lv" created.
```

```bash
lvdisplay
--- Logical volume ---
  LV Path                /dev/SRVIGEOBD25-home-vg/home-lv
  LV Name                home-lv
  VG Name                SRVIGEOBD25-home-vg
  LV UUID                gIenFG-3ol1-W0rb-dbWN-vBfr-mAvc-uH92FC
  LV Write Access        read/write
  LV Creation host, time SRVIGEOBD25, 2024-11-18 17:07:08 +0100
  LV Status              available
  # open                 0
  LV Size                <32,00 GiB
  Current LE             8191
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           254:2

  --- Logical volume ---
  LV Path                /dev/SRVIGEOBD25-vg/root
  LV Name                root
  VG Name                SRVIGEOBD25-vg
  LV UUID                gk71ZU-0CYu-C7T0-iLfS-c3vG-NxdZ-03P3mF
  LV Write Access        read/write
  LV Creation host, time SRVIGEOBD25, 2024-11-15 10:23:50 +0100
  LV Status              available
  # open                 1
  LV Size                <23,54 GiB
  Current LE             6026
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           254:0

  --- Logical volume ---
  LV Path                /dev/SRVIGEOBD25-vg/swap_1
  LV Name                swap_1
  VG Name                SRVIGEOBD25-vg
  LV UUID                xp7Z3A-HOtA-bncI-bFSJ-XMa2-FpQX-ALsj5T
  LV Write Access        read/write
  LV Creation host, time SRVIGEOBD25, 2024-11-15 10:23:51 +0100
  LV Status              available
  # open                 2
  LV Size                976,00 MiB
  Current LE             244
  Segments               1
  Allocation             inherit
  Read ahead sectors     auto
  - currently set to     256
  Block device           254:1
```

#### Ajout du système de fichier sur le volume logique LV

```bash
mkfs.ext4 -L home /dev/SRVIGEOBD25-home-vg/home-lv
```

```bash
mke2fs 1.47.0 (5-Feb-2023)
Discarding device blocks: done
Creating filesystem with 8387584 4k blocks and 2097152 inodes
Filesystem UUID: 529c9ce8-cc88-44cf-986a-c1fbeacc6c86
Superblock backups stored on blocks:
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208,
        4096000, 7962624

Allocating group tables: done
Writing inode tables: done
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done

```

#### Montage du système de fichier

Le dossier `/home` n'est pas vide. Il contient le répertoire de l'utilisateur `adminsig`. On va le déplacer temporairement dans le dossier `tmp` avant de monter le volume logique sur `/home` :
```bash
mv /home/adminsig /tmp
```

```bash
mkdir -p /home
mount /dev/SRVIGEOBD25-home-vg/home-lv /home
```

Ou de cette façon :
```bash
mount /dev/mapper/SRVIGEOBD25--home--vg--home--lv /home
```

```bash
duf
```

```bash
Retour
```

On contrôle avec la commande `mount` ou `df` que le système de fichier (FS) est bien monté :
```bash
mount | grep home
```

```bash
df | grep home
```

##### FSTAB pour rendre le montage permanent sur le système

```bash
vi /etc/fstab

# AJOUTER LA LIGNE
/dev/mapper/SRVIGEOBD25-home-vg-home-lv /home ext4 defaults 0 0
```

```bash
systemctl daemon-reload
```

Vérification de la validité du fichier `/etc/fstab` :
```bash
findmnt --verify --verbose

/
   [ ] la cible existe
   [ ] options FS : errors=remount-ro
   [ ] la source /dev/mapper/SRVIGEOBD25--vg-root existe
   [ ] le type du système de fichier est ext4
/home
   [ ] la cible existe
   [ ] la source /dev/mapper/SRVIGEOBD25--home--vg-home--lv existe
   [ ] le type du système de fichier est ext4
/boot
   [ ] la cible existe
   [ ] UUID=1e2f9456-e99c-4d2a-a8e9-4d86145ba0f9 traduit en /dev/sda1
   [ ] la source /dev/sda1 existe
   [ ] le type du système de fichier est ext2
none
   [ ] la source /dev/mapper/SRVIGEOBD25--vg-swap_1 existe
   [ ] le type du système de fichier est swap
/media/cdrom0
   [ ] la cible existe
   [ ] options en espace utilisateur : user,noauto
   [ ] la source /dev/sr0 existe
   [W] impossible de détecter le type de système de fichiers du disque (Aucun médium trouvé)

0 erreur d'analyse, 0 erreur, 1 alerte
```

On termine la procédure en replaçant le dossier `adminsig` dans le répertoire `/home` qui est maintenant monter sur le volume logique fraichement configuré :
```bash
mv /tmp/adminsig /home
```

## Cas pratique #02

### Création et montage d'un volume logigue avec point de montage sous `/var`