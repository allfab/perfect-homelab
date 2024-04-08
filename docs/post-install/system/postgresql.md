---
title: Post-Installation - Base de données
icon: material/database
---

# **:material-database: Post-Installation - Base de données PostgreSQL**

## **:simple-postgresql: PostgreSQL sur un dataset ZFS**

Ma réflexion ici est de profiter du pool ZFS en mirroir `rpool` créé à [l'installation de Proxmox](/installation/proxmox/#en-mirroir-avec-zfs-raid1) dans le but d'y stocker les données du cluster PotsgreSQL et de profiter des `snapshots` de ZFS. De plus, on profitera aussi des performances des disques NVMe en mirroir sur lequel est créé le pool `rpool`.

Pour cela, je vais créer un container LCX qui va faire tourner PostgreSQL et utiliser les options de point de montage des containers LXC pour mapper un dataset ZFS que je vais créer en amont et le mapper sur `/var/lib/postgresql` de mon container.

On n'oubliera pas de créer un petit script de sauvegarde des données via `pg_dump` ou autre afin de pouvoir remonter une base en cas de soucis, mais dans un premier temps, on pourra tirer profit de la gestion de rollback des snapshots ZFS.

### **Visualisation du pool ZFS**
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

### **Création de l'ensemble de données (dataset) sur le pool ZFS `rpool`**
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

### **Options du pool ZFS `rpool/pgdata`**
``` shell
# On active la compression
root@homelab:~# zfs set compression=lz4 rpool/pgdata
 
# On désactive le temps d'accès (trop d'écritures...)
root@homelab:~# zfs set atime=off rpool/pgdata
 
# On active les attributs étendus améliorés
root@homelab:~# zfs set xattr=sa rpool/pgdata
 
# Je laisse la valeur par défaut de 128 Ko et voir comment ça se passe. 
root@homelab:~#  zfs set recordsize=128k rpool/pgdata
# Les nombres inférieurs sont potentiellement plus rapides, mais les nombres plus élevés
# ont tendance à obtenir une meilleure compression.
``` 

### **Configuration système**
``` shell
# On notifie à Linux de ne pas utiliser le swap sauf en cas d'absolue nécessité
root@homelab:~#  sysctl -w vm.swappiness=1
# Ajoutez 'vm.swappiness=1' à systctl.conf pour un effet permanent  
root@homelab:~#  echo 'vm.swappiness=1' | tee -a /etc/sysctl.conf
```

### **Installation de PotgreSQL sur un container LXC**

Avant de pouvoir configurer un ensemble de données ZFS (dataset), nous devons installer/configurer Postgres sur un container LXC sous Debian.

Postgres doit exécuter son `init` avant d'être déplacer vers un dataset ZFS.
``` shell
root@homelab:~# sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
root@homelab:~# wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add -
root@homelab:~# apt update && apt install postgresql-16 postgresql-contrib-16
```

Ensuite, il faut stoper le service `postgres`, déplacez ses données vers un emplacement temporaire, créez l'ensemble de données ZFS et déplacez les éléments à nouveau.
``` shell
root@homelab:~# systemctl stop postgresql
 
# Déplacer les données postgres vers temp
root@homelab:~# mv /var/lib/postgresql/16/main/pg_wal /tmp/pg_wal
root@homelab:~# mv /var/lib/postgresql /tmp/postgresql
 
# Création du dataset ZFS
root@homelab:~# zfs create postgres/data -o mountpoint=/var/lib/postgresql
root@homelab:~# zfs create postgres/wal -o mountpoint=/var/lib/postgresql/16/main/pg_wal
 
# On recopie/redéplace le cluster postgres initial
root@homelab:~# cp -r /tmp/postgresql/* /var/lib/postgresql
root@homelab:~# cp -r /tmp/pg_wal/* /var/lib/postgresql/16/main/pg_wal
 
#Réactulisation des permissions
root@homelab:~# chmod -R 0700 /var/lib/postgresql
root@homelab:~# chmod -R 0700 /var/lib/postgresql/16/main/pg_wal
root@homelab:~# chown -R postgres: /var/lib/postgresql
 
# Démarrage du service Postgresql
root@homelab:~# systemctl start postgresql
```

### **Configuration PostgreSQL**

On édite le fichier `/etc/postgresql/16/main/postgresql.conf` pour définir `full_page_writes = off` du côté postgres.
ZFS ne peut pas écrire de pages partielles, donc c'est assez redondant.
``` shell
root@homelab:~#  /etc/postgresql/16/main/postgresql.conf

full_page_writes = off
```

### **Point de montage ZFS**

Ajout du point de montage sur le container LXC après l'installation de PotgreSQL sur le container (penser à stopper le service postgres)

`pct set <ID> -mp0 /host/dir,mp=/container/mount/point`

``` shell
root@homelab:~#  pct set 101 -mp0 /var/lib/postgresql,mp=/var/lib/postgresql
```

### **Gestion des droits entre Proxmox et le container LXC**

Gestion des droits entre l'host Proxmox et le container LXC sur lequel les données vont être montées

``` shell
root@homelab:~#  chown -Rf 10000:10000 /var/lib/postgresql
```


### **Références**

- [Our Experience with PostgreSQL on ZFS ](https://lackofimagination.org/2022/04/our-experience-with-postgresql-on-zfs/)
- [Running bare metal PostgreSQL on ZFS](https://ellie.wtf/notes/postgres-on-zfs)
- [Proxmox: bind mountpoint from host to unprivileged LXC container](https://www.itsembedded.com/sysadmin/proxmox_bind_unprivileged_lxc/)