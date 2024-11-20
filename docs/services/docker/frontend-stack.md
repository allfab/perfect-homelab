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

Pour créer un dataset qui va contenir les données de mes stacks `docker compose`, rien de plus facile :
``` shell
root@morpheus:~# zfs create rpool/docker
```

Avec un point de montage sur la machine hôte (Proxmox) :
``` shell
root@morpheus:~# ls -l /opt/
total 0
root@morpheus:~# mkdir /opt/docker
root@morpheus:~# ls -l /opt/
total 1
drwxr-xr-x 2 root root 2 Apr 25 16:15 docker
root@morpheus:~# zfs create rpool/docker -o mountpoint=/opt/docker
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
rpool/docker                     96K   892G    96K  /opt/docker
rpool/pgdata                  12.7M   895G  12.1M  /var/lib/postgresql
rpool/var-lib-vz               104K   895G   104K  /var/lib/vz
```

### Définition d'un quota

``` shell
root@morpheus:~# zfs set quota=20G rpool/docker

root@morpheus:~# zfs get quota rpool/docker
NAME                  PROPERTY  VALUE  SOURCE
rpool/docker  quota     20G    local
```

### Désactiviation `atime` et `relatime`

``` shell
root@morpheus:~# zfs set atime=off rpool/docker
root@morpheus:~# zfs set relatime=off rpool/docker

root@morpheus:~# zfs get quota rpool/docker
NAME                  PROPERTY  VALUE  SOURCE
rpool/docker  quota     20G    local
```

Ensemble des options :
``` shell
root@morpheus:~# zfs get all rpool/docker
NAME                  PROPERTY              VALUE                  SOURCE
rpool/docker  type                  filesystem             -
rpool/docker  creation              Thu Apr 25 16:16 2024  -
rpool/docker  used                  96K                    -
rpool/docker  available             20.0G                  -
rpool/docker  referenced            96K                    -
rpool/docker  compressratio         1.00x                  -
rpool/docker  mounted               yes                    -
rpool/docker  quota                 20G                    local
rpool/docker  reservation           none                   default
rpool/docker  recordsize            128K                   default
rpool/docker  mountpoint            /opt/docker            local
rpool/docker  sharenfs              off                    default
rpool/docker  checksum              on                     default
rpool/docker  compression           lz4                    inherited from rpool
rpool/docker  atime                 on                     inherited from rpool
rpool/docker  devices               on                     default
rpool/docker  exec                  on                     default
rpool/docker  setuid                on                     default
rpool/docker  readonly              off                    default
rpool/docker  zoned                 off                    default
rpool/docker  snapdir               hidden                 default
rpool/docker  aclmode               discard                default
rpool/docker  aclinherit            restricted             default
rpool/docker  createtxg             455656                 -
rpool/docker  canmount              on                     default
rpool/docker  xattr                 on                     default
rpool/docker  copies                1                      default
rpool/docker  version               5                      -
rpool/docker  utf8only              off                    -
rpool/docker  normalization         none                   -
rpool/docker  casesensitivity       sensitive              -
rpool/docker  vscan                 off                    default
rpool/docker  nbmand                off                    default
rpool/docker  sharesmb              off                    default
rpool/docker  refquota              none                   default
rpool/docker  refreservation        none                   default
rpool/docker  guid                  6310504030969019186    -
rpool/docker  primarycache          all                    default
rpool/docker  secondarycache        all                    default
rpool/docker  usedbysnapshots       0B                     -
rpool/docker  usedbydataset         96K                    -
rpool/docker  usedbychildren        0B                     -
rpool/docker  usedbyrefreservation  0B                     -
rpool/docker  logbias               latency                default
rpool/docker  objsetid              1451                   -
rpool/docker  dedup                 off                    default
rpool/docker  mlslabel              none                   default
rpool/docker  sync                  standard               inherited from rpool
rpool/docker  dnodesize             legacy                 default
rpool/docker  refcompressratio      1.00x                  -
rpool/docker  written               96K                    -
rpool/docker  logicalused           42K                    -
rpool/docker  logicalreferenced     42K                    -
rpool/docker  volmode               default                default
rpool/docker  filesystem_limit      none                   default
rpool/docker  snapshot_limit        none                   default
rpool/docker  filesystem_count      none                   default
rpool/docker  snapshot_count        none                   default
rpool/docker  snapdev               hidden                 default
rpool/docker  acltype               off                    default
rpool/docker  context               none                   default
rpool/docker  fscontext             none                   default
rpool/docker  defcontext            none                   default
rpool/docker  rootcontext           none                   default
rpool/docker  relatime              on                     inherited from rpool
rpool/docker  redundant_metadata    all                    default
rpool/docker  overlay               on                     default
rpool/docker  encryption            off                    default
rpool/docker  keylocation           none                   default
rpool/docker  keyformat             none                   default
rpool/docker  pbkdf2iters           0                      default
rpool/docker  special_small_blocks  0                      default
```

## Création d'un dataset `frontend-stack` qui va hérité des options du dataset `rpool/docker`

``` shell
root@morpheus:~# mkdir /opt/docker/frontend-stack
root@morpheus:~# zfs create rpool/docker/frontend-stack -o mountpoint=/opt/docker/frontend-stack
```

## Partage de dossier entre la machine hôte et le conteneur

Ajout du point de montage sur le conteneur LXC :
``` shell
root@morpheus:~# pct set 200 -mp0 /opt/docker/frontend-stack,mp=/opt/docker/frontend-stack
```

## Gestion des droits entre Proxmox et le container LXC

Gestion des droits entre l'host Proxmox et le container LXC sur lequel les données vont être montées

Sur la machine hôte (Proxmox) pour l'utilisateur `root` :
``` shell
root@morpheus:~# chown -Rf 100000:100000 /opt/docker/frontend-stack
```
