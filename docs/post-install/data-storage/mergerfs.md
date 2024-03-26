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
