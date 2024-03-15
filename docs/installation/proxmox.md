---
title: INSTALLATION - Proxmox
icon: simple/proxmox
---

# **INSTALLATION - Proxmox**

![proxmox](../../assets/images/logo/proxmox.svg)


## **Basique avec options LVM**

### **:material-lightbulb: Objectif**

- Installer un serveur Proxmox,
- Par défaut, l'installation de Proxmox crée un groupe de volumes (VG) appelé `pve` et des volumes logiques (LV) associés appelés `root`, `data` et `swap`. Dans notre cas, nous allons faire une installation sans volume de données `local-lvm` de type `LVM-Thin`. Pour contrôler la taille de ces volumes, il est possible de modifier les options avancées via cette boîte de dialogue : 
<figure markdown="span">
  ![proxmox-lvm-advanced-options](../../assets/images/proxmox/basic/proxmox-lvm-advanced-options.png){ width="400" }
  <figcaption>Options avancées LVM</figcaption>
</figure>

- Le but est de configurer ce volume logique `/dev/pve-data` en post installation afin de pouvoir avoir la main totale sur celui-ci,
- Nous allons aussi désactiver le stockage par défaut `local (/var/lib/vz)` qui se trouve sur notre volume logique `/dev/pve-root`.

!!! question "Pourquoi ?"

    Ce stockage est un stockage de type `Directory` dans le répertoire `/var/lib/vz` qui se trouve sur le volume logique `pve-root` de notre serveur, c'est-à-dire sur la partition système de notre serveur. Avec la montée en charge de ce dernier, cet espace de stockage va se remplir d'images ISO, de modèles de container LXC ou encore d'image disque de nos VMs/CTs et occuper tout l'espace disque dédié au système d'exploitation. **Ce que l'on ne veut pas qu'il arrive !**


### **:material-list-box: Installation**

- Booter sur Ventoy en ayant préalablement télécharger et copier [l'image ISO de Proxmox Virtual Environment](https://enterprise.proxmox.com/iso/proxmox-ve_8.1-2.iso),
- Installation de Proxmox :
<figure markdown="span">
  ![proxmox-graphical-installation](../../assets/images/proxmox/basic/proxmox-install-graphical.png){ width="600" }
  <figcaption>Installation de Proxmox en mode graphique</figcaption>
</figure>
<figure markdown="span">
  ![proxmox-install-boot](../../assets/images/proxmox/basic/proxmox-install-boot.png){ width="600" }
  <figcaption>Séquence de boot</figcaption>
</figure>
<figure markdown="span">
  ![proxmox-install-license](../../assets/images/proxmox/basic/proxmox-install-license.png){ width="600" }
  <figcaption>Validez la license</figcaption>
</figure>
<figure markdown="span">
  ![proxmox-install-harddisk-options-01](../../assets/images/proxmox/basic/proxmox-install-harddisk-options-01.png){ width="600" }
  <figcaption>Choisir le disque cible pour l'installation</figcaption>
</figure>

- Suivant la taille de notre disque dur d'installation, configurer l'installation avec ses valeurs d'options avancées de configuration LVM :
    - `hdsize = 128GO`,
    - `swapsize = 8GO`,
    - `maxroot = 64GO`,
    - **`maxvz = 0GO`** ***Ici, cela permet de spécifier à l'installateur de ne pas créer de stockage LVM***,
    - `minfree = 16GO`
<figure markdown="span">
  ![proxmox-install-harddisk-options-02](../../assets/images/proxmox/basic/proxmox-install-harddisk-options-02.png){ width="600" }
  <figcaption>Options avancées de configuration LVM</figcaption>
</figure>
<figure markdown="span">
  ![proxmox-install-network](../../assets/images/proxmox/basic/proxmox-install-network.png){ width="600" }
  <figcaption>Configuration du réseau</figcaption>
</figure>
<figure markdown="span">
  ![proxmox-install-timezone](../../assets/images/proxmox/basic/proxmox-install-timezone.png){ width="600" }
  <figcaption>Configuration de la zone de temps</figcaption>
</figure>
<figure markdown="span">
  ![proxmox-install-summary](../../assets/images/proxmox/basic/proxmox-install-summary.png){ width="600" }
  <figcaption>Récapitulatif de l'installation et on valide l'installation</figcaption>
</figure>

<figure markdown="span">
  ![proxmox-install-loadbar-01](../../assets/images/proxmox/basic/proxmox-install-loadbar-01.png){ width="600" }
  <figcaption>Installation en cours...<figcaption>
</figure>
<figure markdown="span">
  ![proxmox-install-loadbar-02](../../assets/images/proxmox/basic/proxmox-install-loadbar-02.png){ width="600" }
  <figcaption>Installation en cours...<figcaption>
</figure>
<figure markdown="span">
  ![proxmox-install-loadbar-03](../../assets/images/proxmox/basic/proxmox-install-loadbar-03.png){ width="600" }
  <figcaption>Installation en cours...<figcaption>
</figure>

<figure markdown="span">
  ![proxmox-install-complete](../../assets/images/proxmox/basic/proxmox-install-complete.png){ width="600" }
  <figcaption>Installation terminée<figcaption>
</figure>


<figure markdown="span">
  ![proxmox-gui](../../assets/images/proxmox/basic/proxmox-gui.png){ width="600" }
  <figcaption>https://192.168.10.5:8006/<figcaption>
</figure>


## **En mirroir avec ZFS & RAID1**

### **:material-lightbulb: Objectif**

Coming soon

### **:material-list-box: Installation**

- Booter sur Ventoy en ayant préalablement télécharger et copier [l'image ISO de Proxmox Virtual Environment](https://enterprise.proxmox.com/iso/proxmox-ve_8.1-2.iso),
- Installation de Proxmox :
<figure markdown="span">
  ![proxmox-graphical-installation](../../assets/images/proxmox/basic/proxmox-install-graphical.png){ width="600" }
  <figcaption>Installation de Proxmox en mode graphique</figcaption>
</figure>
<figure markdown="span">
  ![proxmox-install-boot](../../assets/images/proxmox/basic/proxmox-install-boot.png){ width="600" }
  <figcaption>Séquence de boot</figcaption>
</figure>
<figure markdown="span">
  ![proxmox-install-license](../../assets/images/proxmox/basic/proxmox-install-license.png){ width="600" }
  <figcaption>Validez la license</figcaption>
</figure>
<figure markdown="span">
  ![proxmox-zfs-disks-options.png](../../assets/images/proxmox/zfs-mirror-raid1/proxmox-zfs-disks-options.png){ width="600" }
  <figcaption>Cliquez sur <b>Options</b> pour paramétrer l'installation en <b>ZFS Mirroir RAID1</b></figcaption>
</figure>

<figure markdown="span">
  ![proxmox-zfs-disks-options-raid1-01](../../assets/images/proxmox/zfs-mirror-raid1/proxmox-zfs-disks-options-raid1-01.png){ width="600" }
  <figcaption>Options avancées de configuration ZFS<br />Choisir <b>zfs (RAID1)</b> pour une installation en mirroir avec 2 disques</figcaption>
</figure>

<figure markdown="span">
  ![proxmox-zfs-disks-options-raid1-02](../../assets/images/proxmox/zfs-mirror-raid1/proxmox-zfs-disks-options-raid1-02.png){ width="600" }
  <figcaption>Dans l'onglet <b>Disk Setup</b>, mapper les 2 disques qui vont nous servir pour l'installation de Proxmox. Ici, <b>/dev/sda et /dev/sdb</b></figcaption>
</figure>

<figure markdown="span">
  ![proxmox-zfs-disks-options-raid1-advanced](../../assets/images/proxmox/zfs-mirror-raid1/proxmox-zfs-disks-options-raid1-advanced.png){ width="600" }
  <figcaption>Dans l'onglet <b>Advanced Options</b>, j'ai juste modifié l'option de compression <b>compress</b> en <b>lz4</b></figcaption>
</figure>

<figure markdown="span">
  ![proxmox-install-timezone](../../assets/images/proxmox/zfs-mirror-raid1/proxmox-install-timezone.png){ width="600" }
  <figcaption>Configuration de la zone de temps</figcaption>
</figure>

<figure markdown="span">
  ![proxmox-install-password](../../assets/images/proxmox/zfs-mirror-raid1/proxmox-install-password.png){ width="600" }
  <figcaption>Mot de passe et contact</figcaption>
</figure>

<figure markdown="span">
  ![proxmox-install-network](../../assets/images/proxmox/zfs-mirror-raid1/proxmox-install-network.png){ width="600" }
  <figcaption>Configuration du réseau</figcaption>
</figure>

<figure markdown="span">
  ![proxmox-install-summary](../../assets/images/proxmox/zfs-mirror-raid1/proxmox-install-summary.png){ width="600" }
  <figcaption>proxmox-install-summary</figcaption>
</figure>

<figure markdown="span">
  ![proxmox-install-loadbar-01](../../assets/images/proxmox/zfs-mirror-raid1/proxmox-install-loadbar-01.png){ width="600" }
  <figcaption>Installation en cours...</figcaption>
</figure>

<figure markdown="span">
  ![proxmox-install-loadbar-02](../../assets/images/proxmox/zfs-mirror-raid1/proxmox-install-loadbar-02.png){ width="600" }
  <figcaption>Installation en cours...</figcaption>
</figure>

<figure markdown="span">
  ![proxmox-install-ok](../../assets/images/proxmox/zfs-mirror-raid1/proxmox-install-ok.png){ width="600" }
  <figcaption>Installation achevée</figcaption>
</figure>

<figure markdown="span">
  ![proxmox-gui](../../assets/images/proxmox/zfs-mirror-raid1/proxmox-gui.png){ width="600" }
  <figcaption>Interface d'administration</figcaption>
</figure>
