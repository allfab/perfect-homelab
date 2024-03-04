---
title: L'envers du décor
icon: material/panorama-variant
---

# **:material-panorama-variant: L'envers du décors**

Mon objectif est donc de créer un serveur pour héberger mes services et mes données. ***Un serveur évolutif, non fermé et basé sur des technologies 100 % open-source.***

En me documentant sur mon projet et comment j'allais arriver à mes fins et construire ce NAS, je suis tombé sur beaucoup de sujets, tutos, etc... Et puis, un beau jour, j'ai ouvert un lien qui parlait du **[Perfect Media Server](https://perfectmediaserver.com/)** !

***Et la, bingo !*** C'est exactement la documentation qu'il me fallait ! Vous aurez donc compris que mon projet est fortement inspiré de ce qu'a réalisé [Alex Kretzschmar](https://www.linkedin.com/in/alex-kretzschmar/) (aka [@IronicBadger](https://twitter.com/ironicbadger)).

Toutes les technologies utilisées dans le processus de création de son NAS me conviennent et c'est comme ça que je me suis retrouvé à écrire ma documentation pour ma propre machine.

On va donc retrouver sous le capot :

- **Proxmox Virtual Environment** : ***Installation en baremetal*** - [Proxmox Virtual Environment](https://www.proxmox.com/en/proxmox-virtual-environment/overview) est une plate-forme complète de gestion de serveurs open source pour la virtualisation d'entreprise. Il intègre étroitement l'hyperviseur KVM et les conteneurs Linux (LXC), la fonctionnalité de stockage et de réseau définie par logiciel, sur une plate-forme unique. Avec une interface utilisateur basée sur le web, vous pouvez gérer les machines virtuelles et les conteneurs, la haute disponibilité pour les clusters ou les outils de reprise après sinistre intégrés avec facilité. **Par conséquent, il y a une énorme marge de manœuvre pour l'évolutivité du serveur.**

!!! success "Proxmox est le bon choix pour vous si :"

    1. Vous aimez une distribution Linux stable qui est Debian,
    2. Vous voulez faire fonctionner des machines virtuelles et des conteneurs LXC,
    3. Vous voulez utiliser la technologie ZFS,
    4. Vous souhaitez regrouper plusieurs serveurs,
    5. Vous souhaitez aller plus loin avec [Proxmox Backup Server](https://www.proxmox.com/en/proxmox-backup-server/overview).


- **Pool de stockage** : Installation basique ou en mirroir avec ZFS en RAID1 pour le système d'exploitation (Proxmox) & [MergerFS](https://github.com/trapexit/mergerfs) pour les données, 
- **Sauvegarde** : [SnapRAID](http://www.snapraid.it/) pour la parité des données,
- **Partage** : NFS
- **Services** : VMs et/ou containers LXC + containers Docker.

<div class="grid cards" markdown>

-   :material-server:{ .lg .middle } **Matériel**

    ---

    Il faut bien attaquer par le début ! Voici la liste du matériel que j'ai acquis pour ce projet.

    [:octicons-arrow-right-24: En savoir plus](/perfect-homelab/tech-stack/hardware/)

-   :material-floppy:{ .lg .middle } **Guide d'installation**

    ---

    Guides pour l'installation de Proxmox Virtual Environment et de l'ensemble des outils du serveur.

    [:octicons-arrow-right-24: Débuter l'installation](/perfect-homelab/installation/proxmox/)

-   :material-folder-open:{ .lg .middle } **Post-installation**

    ---

    Le vrai coeur du système est ici !

    [:octicons-arrow-right-24: Lire la documentation](/perfect-homelab/post-install/)

-   :material-docker:{ .lg .middle } **Services**

    ---

    Créons l'ensemble de nos services !

    [:octicons-arrow-right-24: Explorez l'ensemble des services](/perfect-homelab/services/)

</div>