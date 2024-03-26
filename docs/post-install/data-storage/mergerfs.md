---
title: mergerFS
icon: material/set-merge
---

# **mergerFS**

Mergerfs est l'outil magique de notre NAS ! Voici la procédure d'installation.

L'installation dans Ubuntu ou Debian peut être effectuée à l'aide d'`apt` mais la version dans les dépôts est généralement toujours un peu à la traîne. 

Pour installer `mergerFS`, on va préférer utiliser le fichier *.deb que l'on peut télécharger sur le dépôt [Github du développeur : https://github.com/trapexit/mergerfs/releases](https://github.com/trapexit/mergerfs/releases)

## **Installation sous debian Bookworm (12)**

``` shell
~$ wget https://github.com/trapexit/mergerfs/releases/download/2.39.0/mergerfs_2.39.0.debian-bookworm_amd64.deb
~$ sudo dpkg -i mergerfs_2.39.0.debian-bookworm_amd64.deb
```

On vérifie que l'installation est effective :

``` shell
~$ apt list mergerfs
Listing... Done
```


## **Configuration des disques dur**

La section suivante détaille les étapes pour identifier, monter et partitionner les disques durs de votre système.

### Monter ses disques manuellement

Afin d'utiliser les disques durs présents sur notre NAS, notre système d'exploitation doit les monter. Le **montage** signifie que nous fournissons au système d'exploitation des instructions sur la façon de lire ou d'écrire des données sur un lecteur spécifique. Afin d'être le plus compatible avec `mergerFS`, il est conseillé de configurer les disques à utiliser avec notre NAS avec une unique partition et à la formater avec un seul système de fichiers qui s'étend sur l'intégralité du disque, souvent `ext4` ou `xfs`, puis à les monter sur le sytème.

Il y a de multiples systèmes de fichiers disponibles sous Linux et il n’y a pas de bon ou de mauvais choix. Cependant, on recommande l'`ext4` ou `xfs` pour rester dans des choses simples.

!!! info
    N'oubliez pas qu'avec mergerFS, on peut mélanger et faire correspondre en toute sécurité les systèmes de fichiers et les tailles de lecteur, **ce qui fait partie de sa véritable magie**. 
    
    Cela signifie que vous n'avez pas à trop vous soucier de choisir exactement le bon système de fichiers dès le départ, **car vous n'êtes pas enfermé**.


### Identification des disques durs

Répertoriez tous les lecteurs d'un système avec :

``` shell
~$ sudo ls /dev/disk/by-id
```
Voici la sortie de cette commande :

```
~# sudo ls /dev/disk/by-id
ata-HGST_HDN728080ALE604_R6GPPDTY                     ata-WDC_WD100EMAZ-00WJTA0_2YJ373DD
ata-SPCC_Solid_State_Disk_BA1B0788165300033582        ata-WDC_WD100EMAZ-00WJTA0_2YJ373DD-part1
```

Cette liste va nous permettre de mapper les lecteurs éphémères tels que `/dev/sda` et `ata-HGST_HDN728080ALE604_R6GPPDTY` qui est l'ID. du disque dur. 

Nous pouvons le faire en utilisant `ls -la /dev/disk/by-id/ata-HGST_HDN728080ALE604_R6GPPDTY`.

La sortie suivante est générée :

```
~# ls -la /dev/disk/by-id/ata-HGST_HDN728080ALE604_R6GPPDTY
lrwxrwxrwx 1 root root 9 Sep  9 23:08 /dev/disk/by-id/ata-HGST_HDN728080ALE604_R6GPPDTY -> ../../sda
```

Par conséquent, nous pouvons vérifier que `/dev/sda` est mappé sur ce lecteur physique.

!!! warning
    N'utilisez jamais `/dev/sdX` comme solution à long terme pour l'identification du lecteur, car ces identifiants peuvent changer et changent sans avertissement en raison d'autres modifications matérielles, mises à niveau du noyau, etc...
    
    L'identifiant `/dev/disk/by-id` est lié à cet élément spécifique du matériel, par modèle de lecteur et numéro de série et **ne changera donc jamais**.


### Partitionnement

Avant de créer une partition sur un disque dur fraichement sorti de son emballage, assurez-vous de l'avoir « gravé » comme explicité ici : [Rituel de gravure de disque dur neuf]().

!!! warning 
    **ATTENTION** - Nous sommes sur le point d'effectuer des étapes destructrices sur la table de partition du lecteur. S'il y a des données existantes sur ce disque, elles seront effacées. **Assurez-vous d'être prudent et réfléchi auquel cas vous allez perdre vos données !**

Les étapes suivantes nécessiteront un accès root. En utilisant notre exemple de lecteur de la section précédente, nous utiliserons `cfdisk` et/ou `sgdisk` pour créer une nouvelle partition et un nouveau système de fichiers. 

Suppression des tables de partition (le volume apparaît comme sans table de partition) :
``` shell
sudo sgdisk /dev/sdb -Z
```

Création d'une table de partition gpt et vérification du disque :
``` shell
sudo sgdisk /dev/sda -v
Creating new GPT entries.

No problems found. 31258557 free sectors (14.9 GiB) available in 1
segments, the largest of which is 31258557 (14.9 GiB) in size.
```
!!! note 
    En l'absence de table de partition, les commandes de sgdisk créent préalablement une table de partition lorsque c'est nécessaire à l'exécution de la commande


### Création du système de fichiers
Créez un système de fichiers ext4 (remplacez X par votre lettre de lecteur) :
``` shell
sudo mkfs.ext4 /dev/sda1

sudo mkfs.ext4 -L PARITY /dev/sda1
```
Votre nouveau disque est maintenant formaté et prêt à stocker des données.


### Points de montage

Les points de montage sont l'endroit où le système d'exploitation monte une partition de disque spécifique. Par exemple, vous pouvez avoir plusieurs partitions sur le même disque montées à différents endroits pour des raisons de redondance ou de performances. Pour nos besoins ici, nous garderons les choses simples en montant chaque partition de disque de données une par une.

En supposant que le test précédent s'est bien passé, il est temps de proposer un schéma de dénomination des points de montage. Nous avons recommandé /mnt/diskN car il simplifie l'entrée fstab pour les fusions grâce à la prise en charge des caractères génériques (nous en reparlerons prochainement). 

Par exemple:
``` shell
mkdir /mnt/parity
mount /dev/sda1 /mnt/parity
# ON PEUT AUSSI MONTER LA PARTITION VIA SON ID
# ls -la /dev/disk/by-id/
mount /dev/disk/by-id/ata-VBOX_HARDDISK_VB0e6c06d6-e8cfeb66-part1 /mnt/parity
# ON PEUT ENCORE MONTER LA PARTITION VIA SON UUID
# ls -la /dev/disk/by-uuid/
mount /dev/disk/by-uuid/d82fb001-61b0-4051-836c-d8ed0bf4c613 /mnt/parity

mkfs.ext4 -L DISK1 /dev/sdc1
mkfs.ext4 -L DISK2 /dev/sdd1
mkdir /mnt/disk{1,2}
mount /dev/sdc1 /mnt/disk1
mount /dev/sdd1 /mnt/disk2

mkdir /mnt/storage
```

#### Entrées FSTAB

Ensuite, nous devons créer une entrée dans `/etc/fstab`. Ce fichier indique à votre système d'exploitation comment, où et quels disques monter.  Cela semble un peu complexe mais une entrée fstab est en fait assez simple et se décompose en :

- `device` > `mountpoint` > `filesystem` > `options` > `dump` >`fsck` - documentation fstab.

##### Via l'ID des disques durs

``` shell
#/etc/fstab

/dev/disk/by-id/ata-VBOX_HARDDISK_VB0e6c06d6-e8cfeb66-part1 /mnt/parity ext4 defaults 0 0
/dev/disk/by-id/ata-VBOX_HARDDISK_VB8156f647-895029ce-part1 /mnt/disk1 ext4 defaults 0 0
/dev/disk/by-id/ata-VBOX_HARDDISK_VBc37019e7-f48bd93f-part1 /mnt/disk2 ext4 defaults 0 0

# Notez que mergerfs ne monte pas le lecteur de parité, il monte uniquement /mnt/disk*.
# Mergerfs n'a rien à voir avec la parité, pour cela, nous utilisons SnapRAID.
/mnt/disk* /mnt/storage fuse.mergerfs defaults,nonempty,cache.files=off,category.create=epmfs,moveonenospc=true,dropcacheonclose=true,minfreespace=5G,fsname=mergerfs 0 0

```

##### Via l'UUID des disques durs

``` shell
#/etc/fstab

UUID="d82fb001-61b0-4051-836c-d8ed0bf4c613" /mnt/parity ext4 defaults 0 0
UUID="6b7d93a0-e894-49ff-a7db-d8bfa1a57645" /mnt/disk1 ext4 defaults 0 0
UUID="471e82a7-a963-4bbb-b144-6eb3101390a0" /mnt/disk2 ext4 defaults 0 0

# Notez que mergerfs ne monte pas le lecteur de parité, il monte uniquement /mnt/disk*.
# Mergerfs n'a rien à voir avec la parité, pour cela, nous utilisons SnapRAID.
/mnt/disk* /mnt/storage fuse.mergerfs defaults,nonempty,cache.files=off,category.create=epmfs,moveonenospc=true,dropcacheonclose=true,minfreespace=5G,fsname=mergerfs 0 0
```

##### Options de montage de mergerfs

La documentation : [https://github.com/trapexit/mergerfs?tab=readme-ov-file#options](https://github.com/trapexit/mergerfs?tab=readme-ov-file#options) et le rappel de [la politique de stockage utilisée](/tech-stack/processus/#mergerfs)

- **minfreespace = SIZE** :
	- valeur d'espace minimale utilisée pour les stratégies de création. Peut être remplacé par une option spécifique à la branche. Comprend « K », « M » et « G » comme représentant respectivement le kilo-octet, le mégaoctet et le gigaoctet. (par défaut : 4G)
- **moveonenospc = BOOL|POLICY** : 
	- Lorsqu'elle est activée si une écriture échoue avec ENOSPC (aucun espace disponible sur l'appareil) ou EDQUOT (quota de disque dépassé), la stratégie sélectionnée s'exécutera pour trouver un nouvel emplacement pour le fichier. Une tentative de déplacement du fichier vers cette branche se produira (en gardant toutes les métadonnées possibles) et en cas de succès, l'original sera dissocié et l'écriture sera réessayée. (par défaut : faux, vrai = mfs)
- **dropcacheonclose = BOOL** :
    - Lorsqu'il est demandé de fermer un fichier, appelez d'abord posix_fadvise pour indiquer au noyau que nous n'avons plus besoin des données et qu'il peut supprimer son cache. Recommandé lorsque cache.files=partial|full|auto-full|per-process pour limiter la double mise en cache. (par défaut : faux)
- **category.action = POLICY** :
    - Définit la politique de toutes les fonctions FUSE dans la catégorie d'action. (par défaut : epall)
- **category.create = POLICY** :
    - Définit la politique de toutes les fonctions FUSE dans la catégorie de création. (par défaut : epmfs)
- **category.search = POLICY** :
    - Définit la politique de toutes les fonctions FUSE dans la catégorie de recherche. (par défaut : ff)
- **cache.files = libfuse|off|partial|full|auto-full|per-process** :
    - Mode de mise en cache des pages de fichiers (par défaut : libfuse)
- **fsname = STR** :
    - Définit le nom du système de fichiers tel qu'il apparaît dans mount, df, etc. La valeur par défaut est une liste des chemins sources concaténés avec le préfixe commun le plus long supprimé.

Afin de recharger les nouvelles entrées `fstab` que nous avons créées et de les vérifier avant de redémarrer, montons-les
``` shell
sudo mount -a
```

Vérifiez ensuite les points de montage avec `df -h` ou enocre `duf` :
``` shell
output
```

### Exemple d'arborescence des fichiers

``` shell
root@homelab:/# tree /mnt/disk1

/mnt/disk1/
├── lost+found
└── media
    ├── musics
    ├── photos
    └── videos

root@homelab:/# tree /mnt/disk2
/mnt/disk2
├── backups
│   └── proxmox
├── docker
│   └── appdata
└── lost+found

root@homelab:/# tree /mnt/storage
/mnt/storage/
├── backups
│   └── proxmox
├── docker
│   └── appdata
├── lost+found
└── media
    ├── musics
    ├── photos
    └── videos
```
