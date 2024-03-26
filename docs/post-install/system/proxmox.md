---
title: Post-Installation - Proxmox
icon: simple/proxmox
---

# **:material-note-edit:POST-INSTALLATION - Proxmox - Configuration**

## **:material-source-repository: Désactiver les dépôts pve-enterprise et ceph**

La première chose à faire dans la configuration de Proxmox, est de désactiver le dépôt PVE Entreprise et Ceph si on n'en a pas l'utilité.

On peut le faire de 2 façons différentes :

- Via l'interface graphique
- Ou en ligne de commande

### :material-television-guide: Via l'interface graphique

Se rendre sous ***homelab > Updates > Repositories*** :

<figure markdown="span">
  ![pve-updates-repo](../../assets/images/proxmox/post-install/pve-updates-repo.png){ width="600" }
  <figcaption>homelab > Updates > Repositories</figcaption>
</figure>

Pour le dépot ***https://entreprise.proxmox.com/debian/pve*** :

<figure markdown="span">
  ![disable-pve-enterprise-and-ceph-repositories](../../assets/images/proxmox/post-install/disable-pve-enterprise-and-ceph-repositories-01.png){ width="600" }
  <figcaption>On retrouve cette interface</figcaption>
</figure>

<figure markdown="span">
  ![disable-pve-enterprise-repositorie-01](../../assets/images/proxmox/post-install/disable-pve-enterprise-repositorie-01.png){ width="600" }  
  ![disable-pve-enterprise-repositorie-02](../../assets/images/proxmox/post-install/disable-pve-enterprise-repositorie-02.png){ width="600" }
  <figcaption>On sélectionne la ligne <b><i>https://entreprise.proxmox.com/debian/pve</i></b> et on la désactive en cliquant sur le bouton <b><i>Disable</i></b> qui se trouve au-dessus</figcaption>  
</figure>

Pour le dépot ***https://entreprise.proxmox.com/debian/ceph-quincy*** :

On réitère les étapes ci-dessus en prenant le soin de bien sélectionner le dépôt ***https://entreprise.proxmox.com/debian/ceph-quincy***

### :material-console: En ligne de commande

On se connect en SSH sur le serveur Proxmox ou via le Shell sur l'interface graphique. 
On va venir éditer ce fichier et commenter la ligne :
``` shell
$ vi /etc/apt/sources.list.d/pve-enterprise.list

# deb https://enterprise.proxmox.com/debian/pve bookworm pve-enterprise
```
 Pareil pour le dépôt Ceph :
``` shell
$ vi /etc/apt/sources.list.d/ceph.list

# deb https://enterprise.proxmox.com/debian/ceph-quincy bookworm enterprise
```

## **:material-source-repository: Activer le dépôt pve-no-subscription**

<figure markdown="span">
  ![XXXXX](../../assets/images/proxmox/post-install/XXXXX){ width="600" }
  <figcaption>XXXXX</figcaption>
</figure>

## **:material-source-repository: Désactiver le stockage par défaut local**

<figure markdown="span">
  ![local-storage](../../assets/images/proxmox/post-install/local-storage.png){ width="600" }
  <figcaption>Stockage à désactiver</figcaption>
</figure>

### :material-television-guide: Via l'interface graphique

On sélectionne la ligne correspondant au stockage que l'on veut désactiver, on clique sur `Edit` et on décoche `Enable` et on valide.

### :material-console: En ligne de commande

Pour désactiver le stockage `local` sur `/var/lib/vz` :
``` shell
$ pvesm set local --disable 0
```

Pour activer le stockage `local` sur `/var/lib/vz` :
``` shell
$ pvesm set local --disable 0
```

Référence : [https://pve.proxmox.com/wiki/Storage#_using_the_command_line_interface](https://pve.proxmox.com/wiki/Storage#_using_the_command_line_interface)

## **:material-source-repository: Partitionner le reste de l'espace disque restant afin de créer le stockage LVM-Thin**

Coming soon

## **:material-usb-flash-drive: Stocker les images iso des VMS/CTs**

Afin de ne pas surcharger les espaces de stockage de Proxmox, j'ai opté pour la mise en place d'un stockage sur clé USB pour stocker les images ISOs des VMs ou encore mes templates de les containers LXC.

A cette fin, il faut brancher uné clé USB sur la machine. Moi, j'ai opté pour uné clé USB 3.2 [SanDisk Ultra Fit de 128GO](https://www.westerndigital.com/fr-fr/products/usb-flash-drives/sandisk-ultra-fit-usb-3-1?sku=SDCZ430-128G-G46) que j'ai branché au ***cul*** de mon serveur. Ce ne sont pas des données très sensibles, si ma clé venait à rendre l'âme, ce sont des données facilement remplaçables.

Procédure :

- Branchez la clé USB sur le serveur,
- Sur le serveur Proxmox, lancez la commande pour détécter/identifier le matériel fraîchement ajouté :
``` shell
root@homelab:~# fdisk -l
Disk /dev/sda: 1 TiB, 1099511627776 bytes, 2147483648 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 30CEC8C0-5E64-4538-BCB6-14419B0A845D

Device       Start        End    Sectors  Size Type
/dev/sda1       34       2047       2014 1007K BIOS boot
/dev/sda2     2048    2099199    2097152    1G EFI System
/dev/sda3  2099200 2147483614 2145384415 1023G Solaris /usr & Apple ZFS


Disk /dev/sdb: 1 TiB, 1099511627776 bytes, 2147483648 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: gpt
Disk identifier: 1938A24D-F234-4DAE-958D-964B26179408

Device       Start        End    Sectors  Size Type
/dev/sdb1       34       2047       2014 1007K BIOS boot
/dev/sdb2     2048    2099199    2097152    1G EFI System
/dev/sdb3  2099200 2147483614 2145384415 1023G Solaris /usr & Apple ZFS


Disk /dev/sdc: 128 GiB, 137438953472 bytes, 268435456 sectors
Disk model: VBOX HARDDISK   
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

- On remarque que notre clé est identifiée sur `/dev/sdc`. On va donc formater la clé et la monter.
- Créer une table de partition GPT avec sgdisk :
``` shell
root@homelab:~# sgdisk -o /dev/sdc
Creating new GPT entries in memory.
The operation has completed successfully.
```

!!! abstract "Note"
    Lorsqu'il est invoqué avec l'option `-o` (ou `--clear`), `sgdisk` efface toute table de partition existante sur le périphérique donné et crée une nouvelle table de partition GPT. Encore une fois, puisque le programme est destiné à être utilisé à partir de scripts, aucun avertissement ne sera émis et aucune confirmation ne sera demandée, il doit donc être utilisé avec précaution.


- Formatage de la clé :    
``` shell
root@homelab:~# mkfs.ext4 /dev/sdc
mke2fs 1.47.0 (5-Feb-2023)
Found a gpt partition table in /dev/sdc
Proceed anyway? (y,N) y
Creating filesystem with 33554432 4k blocks and 8388608 inodes
Filesystem UUID: 01418e86-d606-4d40-acf4-79af5045172c
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
        4096000, 7962624, 11239424, 20480000, 23887872

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: done
```

- Créé un point de montage et monter la clé :
``` shell
root@homelab:~# mkdir /mnt/iso
root@homelab:~# mount /dev/sdc /mnt/iso
root@homelab:~# df -h
Filesystem        Size  Used Avail Use% Mounted on
udev              3.9G     0  3.9G   0% /dev
tmpfs             794M  792K  793M   1% /run
rpool/ROOT/pve-1  985G  1.7G  983G   1% /
tmpfs             3.9G   43M  3.9G   2% /dev/shm
tmpfs             5.0M     0  5.0M   0% /run/lock
rpool             983G  128K  983G   1% /rpool
rpool/var-lib-vz  983G  128K  983G   1% /var/lib/vz
rpool/ROOT        983G  128K  983G   1% /rpool/ROOT
rpool/data        983G  128K  983G   1% /rpool/data
/dev/fuse         128M   16K  128M   1% /etc/pve
tmpfs             794M     0  794M   0% /run/user/0
/dev/sdc          125G   28K  119G   1% /mnt/iso
```
On retrouve bien notre clé USB montée sur `/mnt/iso`. À ce stade, le point de montage est éphémère. Si jamais la machine redémarre, la clé USB ne sera pas remontée automatquement sur `/mnt/iso`. Pour cela, il faut éditer le fichier `/etc/fstab`.


- Rendre persistent le montage via `/etc/fstab` et l'D du disque :
``` shell
root@homelab:~# ls -al /dev/disk/by-id/
total 0
drwxr-xr-x 2 root root 220 Mar 15 15:22 .
drwxr-xr-x 8 root root 160 Mar 15 14:54 ..
lrwxrwxrwx 1 root root   9 Mar 15 14:54 ata-VBOX_HARDDISK_VB4d776a0a-b24c3eb0 -> ../../sda
lrwxrwxrwx 1 root root  10 Mar 15 14:54 ata-VBOX_HARDDISK_VB4d776a0a-b24c3eb0-part1 -> ../../sda1
lrwxrwxrwx 1 root root  10 Mar 15 14:54 ata-VBOX_HARDDISK_VB4d776a0a-b24c3eb0-part2 -> ../../sda2
lrwxrwxrwx 1 root root  10 Mar 15 14:54 ata-VBOX_HARDDISK_VB4d776a0a-b24c3eb0-part3 -> ../../sda3
lrwxrwxrwx 1 root root   9 Mar 15 14:54 ata-VBOX_HARDDISK_VB4fd111ed-7ccb2978 -> ../../sdb
lrwxrwxrwx 1 root root  10 Mar 15 14:54 ata-VBOX_HARDDISK_VB4fd111ed-7ccb2978-part1 -> ../../sdb1
lrwxrwxrwx 1 root root  10 Mar 15 14:54 ata-VBOX_HARDDISK_VB4fd111ed-7ccb2978-part2 -> ../../sdb2
lrwxrwxrwx 1 root root  10 Mar 15 14:54 ata-VBOX_HARDDISK_VB4fd111ed-7ccb2978-part3 -> ../../sdb3
lrwxrwxrwx 1 root root   9 Mar 15 15:22 ata-VBOX_HARDDISK_VBb598f427-91b62771 -> ../../sdc
```
``` shell
root@homelab:~# vi /etc/fstab
# <file system> <mount point> <type> <options> <dump> <pass>
proc /proc proc defaults 0 0

# /mnt/iso
/dev/disk/by-id/ata-VBOX_HARDDISK_VBb598f427-91b62771      /mnt/iso      ext4     defaults     0    0
```

- On redémarre pour vérifier que la configuration est bien effective !

- Si la clé est bien montée au redémarrage, on peut passer à la configuration du stockage sur Proxmox :

<figure markdown="span">
  ![proxmox-storage-iso-directory](../../assets/images/proxmox/post-install/proxmox-storage-iso-directory.gif){ width="800" }
  <figcaption>Configuration du stockage des ISO sur la clé USB</figcaption>
</figure>


## **:simple-postgresql: PostgreSQL sur un dataset ZFS**

Ma réflexion ici est de profiter du pool ZFS en mirroir `rpool` créé à [l'installation de Proxmox](/installation/proxmox/#en-mirroir-avec-zfs-raid1) dans le but d'y stocker les données du cluster PotsgreSQL et de profiter des `snapshots` de ZFS. De plus, on profitera aussi des performances des disques NVMe en mirroir sur lequel est créé le pool `rpool`.

Pour cela, je vais créer un container LCX qui va faire tourner PostgreSQL et utiliser les options de point de montage des containers LXC pour mapper un dataset ZFS que je vais créer en amont et le mapper sur `/var/lib/postgresql` de mon container.

On n'oubliera pas de créer un petit script de sauvegarde des données via `pg_dump` ou autre afin de pouvoir remonter une base en cas de soucis, mais dans un premier temps, on pourra tirer profit de la gestion de rollback des snapshots ZFS.

Référence : [https://lackofimagination.org/2022/04/our-experience-with-postgresql-on-zfs/](https://lackofimagination.org/2022/04/our-experience-with-postgresql-on-zfs/)

- Visualisation du pool ZFS :
``` shell
root@homelab:~# zpool status
  pool: rpool
 state: ONLINE
  scan: scrub repaired 0B in 00:00:34 with 0 errors on Fri Mar 15 14:31:08 2024
config:

        NAME                                             STATE     READ WRITE CKSUM
        rpool                                            ONLINE       0     0     0
          mirror-0                                       ONLINE       0     0     0
            ata-VBOX_HARDDISK_VB4d776a0a-b24c3eb0-part3  ONLINE       0     0     0
            ata-VBOX_HARDDISK_VB4fd111ed-7ccb2978-part3  ONLINE       0     0     0

errors: No known data errors

root@homelab:~# zfs list
NAME               USED  AVAIL  REFER  MOUNTPOINT
rpool             1.70G   983G   104K  /rpool
rpool/ROOT        1.70G   983G    96K  /rpool/ROOT
rpool/ROOT/pve-1  1.70G   983G  1.70G  /
rpool/data          96K   983G    96K  /rpool/data
rpool/var-lib-vz    96K   983G    96K  /var/lib/vz
```

- Création de l'ensemble de données (dataset) sur le pool ZFS `rpool` :
``` shell
root@homelab:~# zfs create rpool/pgdata -o mountpoint=/var/lib/postgresql
root@homelab:~# zfs list
NAME               USED  AVAIL  REFER  MOUNTPOINT
rpool             1.70G   983G   104K  /rpool
rpool/ROOT        1.70G   983G    96K  /rpool/ROOT
rpool/ROOT/pve-1  1.70G   983G  1.70G  /
rpool/data          96K   983G    96K  /rpool/data
rpool/pgdata        96K   983G    96K  /var/lib/postgresql
rpool/var-lib-vz    96K   983G    96K  /var/lib/vz
```

- Options du pool ZFS `rpool/pgdata` :
``` shell
# On active la compression
root@homelab:~# zfs set compression=lz4 rpool/pgdata
 
# On désactive le temps d'accès (trop d'écritures...)
root@homelab:~# zfs set atime=off rpool/pgdata
 
# On active les attributs étendus améliorés
root@homelab:~# zfs set xattr=sa rpool/pgdata
 
# On définit la taille de l'enregistrement ZFS sur 32 Ko (par défaut : 128 Ko)
root@homelab:~#  zfs set recordsize=128k rpool/pgdata
# Je laisse la valeur par défaut de 128 Ko et voir comment ça se passe. 
# Les nombres inférieurs sont potentiellement plus rapides, mais les nombres plus élevés
# ont tendance à obtenir une meilleure compression.
``` 

- Configuration système :
``` shell
# On notifie à Linux de ne pas utiliser le swap sauf en cas d'absolue nécessité
root@homelab:~#  sysctl -w vm.swappiness=1
# Ajoutez 'vm.swappiness=1' à systctl.conf pour un effet permanent  
root@homelab:~#  echo 'vm.swappiness=1' | tee -a /etc/sysctl.conf
```

- Gestion des droits entre l'host Proxmox et le container LXC sur lequel les données vont être montées :

Référence : [https://www.itsembedded.com/sysadmin/proxmox_bind_unprivileged_lxc/](https://www.itsembedded.com/sysadmin/proxmox_bind_unprivileged_lxc/)

``` shell
root@homelab:~#  chown -Rf 10000:10000 /var/lib/postgresql
```

- Ajout du point de montage sur le container LXC après l'installation de PotgreSQL sur le container (penser à stopper le service postgres):

`pct set <ID> -mp0 /host/dir,mp=/container/mount/point`

``` shell
root@homelab:~#  pct set 101 -mp0 /var/lib/postgresql,mp=/var/lib/postgresql
```