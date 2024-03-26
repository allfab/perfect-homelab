---
title: POST-INSTALLATION - Proxmox Virtual Environment
#icon: material/server
---

# **:material-note-edit:POST-INSTALLATION - Proxmox**

## **:material-shield-crown: Administration de l'hyperviseur**

<div class="grid cards" markdown>

-   :material-note-edit:{ .lg .middle } __Configuration Proxmox__
    
    ---

    - [Désactiver les dépôts `pve-enterprise` et `ceph`](#)
    - [Activer le dépôt `pve-no-subscription`](#)
    - [Désactiver le stockage par défaut `local`](#)
    - [Partitionner le reste de l'espace disque restant afin de créer le stockage `LVM-Thin`](#)
    - [XXXXX](#)
    
-   :material-console:{ .lg .middle } __Système__
   
    ---

    - [Installer les paquets indispensables à la vie du serveur](/post-install/system/packages)
    - [Configuration `SSH`](#)
    - [XXXXX](#)

-   :material-harddisk:{ .lg .middle } __Données et stockage__
   
    ---

    - [Installer et configurer `mergerFS`](#)
    - [Installer et configurer `SnapRAID`](#)
    - [XXXXX](#)
    - [XXXXX](#)
    
</div>



<!-- - Création des services sous Proxmox propulsés sous Docker via VMs et/ou CTs : frontend, smarthome, mediaserver, etc...
- Gestion de la sauvegarde via PBS ? -->