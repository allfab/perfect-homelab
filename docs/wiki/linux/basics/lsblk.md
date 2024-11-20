---
title: WIKI - Linux - Commandes de base
icon: material/ssh
tags:
    - Wiki
    - Linux
    - LSBLK
---

# WIKI - Linux - Commandes de base

## **Lister les disques et les partitions du système avec l'utilitaire LSBLK**

### LSBLK

#### Installation sous Debian

``` bash
apt-get install util-linux
```

#### Usage

La commande `lsblk` permet d'obtenir la liste et les caractéristiques des disques et de leurs partitions. La commande ne nécessite pas les droits administrateurs pour être exécutée.  

Afficher des renseignements sur les systèmes de fichiers :
``` bash
lsblk -f
```

``` bash
lsblk -o NAME,TYPE,PARTTYPE,MOUNTPOINT,FSTYPE,FSSIZE,SIZE,FSAVAIL,FSUSE%,ALIGNMENT,UUID,MODEL,SERIAL,PHY-SEC
```

``` bash
lsblk -o NAME,LABEL,TYPE,MOUNTPOINT,FSTYPE,FSSIZE,SIZE,FSAVAIL,FSUSE%,ALIGNMENT,UUID,MODEL,SERIAL,PHY-SEC
```

Retour de la commande :
``` bash

NAME              TYPE PARTTYPENAME     MOUNTPOINTS     FSTYPE      FSVER             SIZE FSAVAIL FSUSE% ALIGNMENT UUID                                   MODEL         SERIAL      PHY-SEC

sda               disk                                                                 22G                        0                                        QEMU HARDDISK drive-scsi0     512

├─sda1            part Linux filesystem /boot           ext4        1.0               953M  797,1M     6%         0 d26adfa3-c7a2-4f78-8730-305a6a6ef842                                 512

└─sda2            part Linux LVM                        LVM2_member LVM2 001         21,1G                        0 7VFATj-KLBq-UU44-1QlS-Uz26-T62h-TJZohf                               512

  ├─rootvg-rootlv lvm                   /               ext4        1.0              19,2G    9,8G    43%         0 805d9569-909c-439f-b596-0f1513144761                                 512

  └─rootvg-swaplv lvm                   [SWAP]          swap        1                 1,9G                        0 18856f92-824a-4d4b-bcc4-472f7c96cddd                                 512

sdb               disk                                                                100G                        0                                        QEMU HARDDISK drive-scsi1     512

└─sdb1            part Linux filesystem /var/lib/docker btrfs                         100G   86,8G    12%         0 603cef1a-f969-4374-b45b-0fe4d460149e                                 512

sr0               rom                                   iso9660     Joliet Extension  628M                        0 2023-10-07-10-32-09-00                 QEMU DVD-ROM  QM00003        2048

```