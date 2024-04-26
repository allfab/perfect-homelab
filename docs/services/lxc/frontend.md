---
title: LXC - Frontend
icon: material/hand-front-left
---

# **LXC - Frontend**

## Création d'un conteneur LXC via Proxmox VE Helper-Scripts

[https://tteck.github.io/Proxmox/](https://tteck.github.io/Proxmox/)

Sur Proxmox :

``` shell
root@morpheus:~# bash -c "$(wget -qLO - https://github.com/tteck/Proxmox/raw/main/ct/docker.sh)"
```

## Configuration du conteneur LXC

...

## Dataset ZFS & Point de montage

### Création de l'ensemble de données (dataset) sur le pool ZFS `rpool`

!!! tip "Dataset ZFS"
    
    Les datasets sont des points de contrôle, ils peuvent être imbriqués et héritent des propriétés de leur parent (appelé « stub »). On peut également les faire pointer vers un dossier sur le système de fichier. C'est ce que l'on appelle les points de montage.

Pour créer un dataset, rien de plus facile :
``` shell
root@morpheus:~# zfs create rpool/docker-appdata
```

Avec un point de montage :
``` shell
root@morpheus:~# ls -l /opt/
total 0
root@morpheus:~# mkdir /opt/docker
root@morpheus:~# ls -l /opt/
total 1
drwxr-xr-x 2 root root 2 Apr 25 16:15 docker
root@morpheus:~# zfs create rpool/docker-appdata -o mountpoint=/opt/docker
```

Vérifions que le dataset a bien été créé :
``` shell
root@morpheus:~# zfs list
NAME                           USED  AVAIL  REFER  MOUNTPOINT
rpool                         3.83G   895G   104K  /rpool
rpool/ROOT                    2.81G   895G    96K  /rpool/ROOT
rpool/ROOT/pve-1              2.81G   895G  2.81G  /
rpool/data                     995M   895G    96K  /rpool/data
rpool/data/subvol-200-disk-0   995M  3.03G   995M  /rpool/data/subvol-200-disk-0
rpool/docker-appdata            96K   895G    96K  /opt/docker
rpool/pgdata                  12.7M   895G  12.1M  /var/lib/postgresql
rpool/var-lib-vz               104K   895G   104K  /var/lib/vz
```

### Définition d'un quota

``` shell
root@morpheus:~# zfs set quota=20G rpool/docker-appdata

root@morpheus:~# zfs get quota rpool/docker-appdata
NAME                  PROPERTY  VALUE  SOURCE
rpool/docker-appdata  quota     20G    local
```

Ensemble des options :
``` shell
root@morpheus:~# zfs get all rpool/docker-appdata
NAME                  PROPERTY              VALUE                  SOURCE
rpool/docker-appdata  type                  filesystem             -
rpool/docker-appdata  creation              Thu Apr 25 16:16 2024  -
rpool/docker-appdata  used                  96K                    -
rpool/docker-appdata  available             20.0G                  -
rpool/docker-appdata  referenced            96K                    -
rpool/docker-appdata  compressratio         1.00x                  -
rpool/docker-appdata  mounted               yes                    -
rpool/docker-appdata  quota                 20G                    local
rpool/docker-appdata  reservation           none                   default
rpool/docker-appdata  recordsize            128K                   default
rpool/docker-appdata  mountpoint            /opt/docker            local
rpool/docker-appdata  sharenfs              off                    default
rpool/docker-appdata  checksum              on                     default
rpool/docker-appdata  compression           lz4                    inherited from rpool
rpool/docker-appdata  atime                 on                     inherited from rpool
rpool/docker-appdata  devices               on                     default
rpool/docker-appdata  exec                  on                     default
rpool/docker-appdata  setuid                on                     default
rpool/docker-appdata  readonly              off                    default
rpool/docker-appdata  zoned                 off                    default
rpool/docker-appdata  snapdir               hidden                 default
rpool/docker-appdata  aclmode               discard                default
rpool/docker-appdata  aclinherit            restricted             default
rpool/docker-appdata  createtxg             455656                 -
rpool/docker-appdata  canmount              on                     default
rpool/docker-appdata  xattr                 on                     default
rpool/docker-appdata  copies                1                      default
rpool/docker-appdata  version               5                      -
rpool/docker-appdata  utf8only              off                    -
rpool/docker-appdata  normalization         none                   -
rpool/docker-appdata  casesensitivity       sensitive              -
rpool/docker-appdata  vscan                 off                    default
rpool/docker-appdata  nbmand                off                    default
rpool/docker-appdata  sharesmb              off                    default
rpool/docker-appdata  refquota              none                   default
rpool/docker-appdata  refreservation        none                   default
rpool/docker-appdata  guid                  6310504030969019186    -
rpool/docker-appdata  primarycache          all                    default
rpool/docker-appdata  secondarycache        all                    default
rpool/docker-appdata  usedbysnapshots       0B                     -
rpool/docker-appdata  usedbydataset         96K                    -
rpool/docker-appdata  usedbychildren        0B                     -
rpool/docker-appdata  usedbyrefreservation  0B                     -
rpool/docker-appdata  logbias               latency                default
rpool/docker-appdata  objsetid              1451                   -
rpool/docker-appdata  dedup                 off                    default
rpool/docker-appdata  mlslabel              none                   default
rpool/docker-appdata  sync                  standard               inherited from rpool
rpool/docker-appdata  dnodesize             legacy                 default
rpool/docker-appdata  refcompressratio      1.00x                  -
rpool/docker-appdata  written               96K                    -
rpool/docker-appdata  logicalused           42K                    -
rpool/docker-appdata  logicalreferenced     42K                    -
rpool/docker-appdata  volmode               default                default
rpool/docker-appdata  filesystem_limit      none                   default
rpool/docker-appdata  snapshot_limit        none                   default
rpool/docker-appdata  filesystem_count      none                   default
rpool/docker-appdata  snapshot_count        none                   default
rpool/docker-appdata  snapdev               hidden                 default
rpool/docker-appdata  acltype               off                    default
rpool/docker-appdata  context               none                   default
rpool/docker-appdata  fscontext             none                   default
rpool/docker-appdata  defcontext            none                   default
rpool/docker-appdata  rootcontext           none                   default
rpool/docker-appdata  relatime              on                     inherited from rpool
rpool/docker-appdata  redundant_metadata    all                    default
rpool/docker-appdata  overlay               on                     default
rpool/docker-appdata  encryption            off                    default
rpool/docker-appdata  keylocation           none                   default
rpool/docker-appdata  keyformat             none                   default
rpool/docker-appdata  pbkdf2iters           0                      default
rpool/docker-appdata  special_small_blocks  0                      default
```

## Partage de dossier entre la machine hôte et le conteneur


Ajout du point de montage sur le conteneur LXC :
``` shell
root@morpheus:~# pct set 200 -mp0 /opt/docker,mp=/opt/docker
```


## Gestion des droits entre Proxmox et le container LXC

Gestion des droits entre l'host Proxmox et le container LXC sur lequel les données vont être montées

Sur la machine hôte (Proxmox) :
``` shell
root@morpheus:~# chown -Rf 100000:100000 /opt/docker
```