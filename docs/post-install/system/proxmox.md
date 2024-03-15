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


### :material-console: En ligne de commande

Pour désactiver le stockage `local` sur `/var/lib/vz` :
``` shell
$ pvesm set local --disable 0
```

Pour activer le stockage `local` sur `/var/lib/vz` :
``` shell
$ pvesm set local --disable 0
```

## **:material-source-repository: Partitionner le reste de l'espace disque restant afin de créer le stockage LVM-Thin**

Coming soon
