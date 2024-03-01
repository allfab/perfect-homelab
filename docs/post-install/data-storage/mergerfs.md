---
title: mergerFS
icon: material/set-merge
---

# **mergerFS**

Mergerfs est l'outil magique qui rend votre NAS parfait ! Voici la procédure d'installation.

L'installation dans Ubuntu ou Debian peut être effectuée à l'aide d'`apt` mais la version dans les dépôts est généralement toujours un peu à la traîne. 

Par exemple, au moment de la rédaction, Debian propose la version `2.XX.X` qui a été publiée en XXXXX 202XXX. La dernière version en date est celle du XX mois 20XX avec la version `2.39.0.`.

Pour installer mergerFS, on va préférer utiliser le fichier *.deb que l'on peut télécharger sur le dépôt [Github du développeur : https://github.com/trapexit/mergerfs/releases](https://github.com/trapexit/mergerfs/releases)

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

Il y a de multiples systèmes de fichiers disponibles osus Linux et il n’y a pas de bon ou de mauvais choix. Cependant, on recommande l'`ext4` ou `xfs` pour rester dans des choses simples.

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
lrwxrwxrwx 1 root root 9 Sep  9 23:08 /dev/disk/by-id/ata-HGST_HDN728080ALE604_R6GPPDTY -> ../../sdc
```

Par conséquent, nous pouvons vérifier que `/dev/sda` est mappé sur ce lecteur physique.

!!! warning
    N'utilisez jamais `/dev/sdX` comme solution à long terme pour l'identification du lecteur, car ces identifiants peuvent changer et changent sans avertissement en raison d'autres modifications matérielles, mises à niveau du noyau, etc...
    
    L'identifiant `/dev/disk/by-id` est lié à cet élément spécifique du matériel, par modèle de lecteur et numéro de série et **ne changera donc jamais**.


### Partitionnement

Avant de créer une partition sur un disque dur fraichement sorti de son emballage, assurez-vous de l'avoir « gravé » comme explicité ici : [Rituel de gravure de disque dur neuf]().

!!! warning 
    **ATTENTION** - Nous sommes sur le point d'effectuer des étapes destructrices sur la table de partition du lecteur. S'il y a des données existantes sur ce disque, elles seront effacées. **Assurez-vous d'être prudent et réfléchi auquel cas vous allez perdre vos données !**

The following steps will require root access. To become the root user type `sudo su`. Using our example drive from the prior section we will use `gdisk` to create a new partition and filesystem. Run `gdisk /dev/sdX` (replacing `sdX` with your drive), for example:

    root@cartman:~# gdisk /dev/sdc
    GPT fdisk (gdisk) version 1.0.5

    Partition table scan:
        MBR: protective
        BSD: not present
        APM: not present
        GPT: not present

Once `gdisk` is loaded we are presented with an interactive prompt `Command (? for help):`. To see all options simply type `?`. In the initial output from gdisk we can see there is no partition table present on this drive - it's a good sanity check you have the right drive before erasing the partition and file allocation tables.

!!! danger
    The following sequence will erase everything on this disk. **MAKE SURE YOU HAVE A BACKUP AND USE CAUTION**

Use the following sequence to create one large partition spanning the entire drive. Note that the keys you need to press are at the start of each heading and the answers to the subsequent questions at the ends of the next few lines.

```
* o - creates a new **EMPTY** GPT partition table (GPT is good for large drives over 3TB)
    * Proceed? (Y/N) - **`Y`**
* n - creates a new partition
    * Partition number (1-128, default 1): **`1`**
    * First sector (34-15628053134, default = 2048) or {+-}size{KMGTP}: **`leave blank`**
    * Last sector (2048-15628053134, default = 15628053134) or {+-}size{KMGTP}: **`leave blank`**
    * Hex code or GUID (L to show codes, Enter = 8300): **`8300`**
* p - (optional) validate 1 large partition to be created
    * Model: HGST HDN728080AL
    * Number  Start (sector)    End (sector)  Size       Code  Name
    * 1       2048              15628053134   7.3 TiB    8300  Linux filesystem
* w - writes the changes made thus far
    * Until this point, gdisk has been non-destructive
    * Confirm that making these changes is OK and the changes queued so far will be executed
```

Next up, we'll create a filesystem on that newly created partition.

!!! info
    Rinse and repeat this step for each new drive as required.

#### Filesystem creation

Create an `ext4` filesystem thus (replace `X` with your drive letter):

    mkfs.ext4 /dev/sdX1

Congratulations! Your new drive is now formatted and ready to store data. 

Move onto the next section 'Existing drive' to learn how to mount it (make it available to the OS for use).

### Existing drives

[Identify](#identifying-drives) the existing drive and take note of the partition you wish to mount. This is usually displayed as `-part1` using `/dev/disk/by-id`.

!!! info
    Ensure you have the correct supporting libraries for your filesystem installed such as `xfsprogs` for XFS.

    With Ubuntu this is achieved via `sudo apt install xfsprogs`.
    
You should now be able to mount the drive manually like so:

    mkdir /mnt/manualdiskmounttest
    mount /dev/disk/by-id/ata-HGST_HDN728080ALE604_R6GPPDTY-part1 /mnt/manualdiskmounttest

Verify that the drive mounted and displays the correct size as expected:

    root@cartman:~# df -h
    Filesystem                        Size  Used Avail Use% Mounted on
    /dev/sdc1                         7.3T  2.8T  4.6T  38% /mnt/manualdiskmounttest

### Mountpoints

Mountpoints are where the OS mounts a specific disk partition. For example, you could have multiple partitions on the same disk mounted to different places for redundancy or performance reasons. For our purposes here we'll keep things simple by mounting each data disk partition one by one.

Assuming the previous test went well, it's time to come up with a mountpoint naming scheme. We recommended `/mnt/diskN` because it makes the `fstab` entry for mergerfs simpler thanks to wildcard support (more on this shortly). For example:

    mkdir /mnt/disk{1,2,3,4}
    mkdir /mnt/parity1 # adjust this command based on your parity setup
    mkdir /mnt/storage # this will be the main mergerfs mountpoint

We also just created `/mnt/storage` in addition to our data disk mountpoints of `/mnt/disk1`, `/mnt/disk2` and so on. `/mnt/storage` will be used by [mergerfs](../02-tech-stack/mergerfs.md) to 'pool' or 'merge' our data disks.

### fstab entries

Next we need to create an entry in `/etc/fstab`.

This file tells your OS how, where and which disks to mount. It looks a bit complex but an fstab entry is actually quite simple and breaks down to `<device> <mountpoint> <filesystem> <options> <dump> <fsck>` - [fstab documentation](https://wiki.archlinux.org/index.php/fstab).

!!! note 
    Note that mergerfs does *not* mount the parity drive, it only mounts `/mnt/disk*`. mergerfs has *nothing to do* with parity, that is what we use SnapRAID for.

Here's what your `/etc/fstab` file might look like with 4 data disks and 1 SnapRAID parity drive. 

```
##/etc/fstab example
/dev/disk/by-id/ata-WDC_WD100EMAZ-00WJTA0_16G0Z7RZ-part1 /mnt/parity1 ext4 defaults 0 0
/dev/disk/by-id/ata-WDC_WD100EMAZ-00WJTA0_16G10VZZ-part1 /mnt/disk1   ext4 defaults 0 0
/dev/disk/by-id/ata-WDC_WD100EMAZ-00WJTA0_2YHV69AD-part1 /mnt/disk2   ext4 defaults 0 0
/dev/disk/by-id/ata-WDC_WD100EMAZ-00WJTA0_2YJ15VJD-part1 /mnt/disk3   ext4 defaults 0 0
/dev/disk/by-id/ata-HGST_HDN728080ALE604_R6GPPDTY-part1  /mnt/disk4   ext4 defaults 0 0

/mnt/disk* /mnt/storage fuse.mergerfs defaults,nonempty,allow_other,use_ino,cache.files=off,moveonenospc=true,dropcacheonclose=true,minfreespace=200G,fsname=mergerfs 0 0
```

In order to reload the new fstab entries you've created and check them before rebooting, use `mount -a`. Then verify the mount points with `df -h`.

```
root@cartman:~# df -h
Filesystem                        Size  Used Avail Use% Mounted on
/dev/sdo2                          59G   22G   34G  39% /
/dev/sdj1                         469G  118G  328G  27% /opt
/dev/sde1                         9.1T  7.1T  2.1T  78% /mnt/disk1
/dev/sdg1                         9.1T  547G  8.6T   6% /mnt/disk2
/dev/sdm1                         9.1T  5.6T  3.6T  62% /mnt/disk3
/dev/sdc1                         7.3T  2.8T  4.6T  38% /mnt/disk4
/dev/sdl1                         9.1T  7.2T  2.0T  79% /mnt/parity1
mergerfs                           34T   24T   10T  69% /mnt/storage
```

If you had any existing files on your data disks they will be visible under `/mnt/storage`.