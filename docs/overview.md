---
title: L'envers du décor
icon: material/panorama-variant
---

# **:material-panorama-variant: L'envers du décors**

Mon objectif est donc de créer un NAS pour héberger mes données multimédia (photos, vidéos, musique) et les services qui permettront de profiter de ces données. ***L'objectif de ce serveur de stockage/NAS est d'être évolutif, non fermé et basé sur des technologies 100 % open-source.***

En me documentant sur mon projet et comment j'allais arriver à mes fins et construire ce NAS, je suis tombé sur beaucoup de sujets, tutos, etc... Et puis, un beau jour, j'ai ouvert un lien qui parlait du **[Perfect Media Server](https://perfectmediaserver.com/)** !

***Et la, bingo !*** C'est exactement la documentation qu'il me fallait ! Vous aurez donc compris que mon projet est fortement inspiré de ce qu'a réalisé [Alex Kretzschmar](https://www.linkedin.com/in/alex-kretzschmar/) (aka [@IronicBadger](https://twitter.com/ironicbadger)).

Toutes les technologies utilisées dans le processus de création de son NAS me conviennent et c'est comme ça que je me suis retrouvé à écrire ma documentation pour ma propre machine.

!!! question "Note"

    À force de lire les nombreux tutos qui documentent le sujet et de voir les innombrables vidéos sous Youtube de mise en place de Homelab avec des partis pris technologiques forts, j'en suis arrivé à ne plus savoir comment allait être structurer le NAS que je voulais mette en place :

    - Quel système d'exploitation installé ?
    - Qu'est-ce qui va être virtualisé ou non ?
    - Quelles sont les briques logicielles qui vont fonctionner en **baremetal** (***directement sur l'hôte et donc sur Proxmox et donc sur Debian Bookworm***) ?
    - Quelles sont les briques logicielles qui vont être propulsées via **Docker** ?
    - D'ailleurs, comment installe-t-on **Docker** ? Sur une **VM**, un container **LXC** ?

    Ce sont toutes des questions légitimes sur lesquelles je me suis penché assez longtemps pour trouver la meilleure granularité du système et suivant mes compétences (je ne suis pas sysadmin ou devops !) :

    - Pour la mise à jour système,
    - Pour l'upgrade matériel,
    - Pour l'évolutivité des services proposés.

    Vous retrouverez donc mes choix technologiques ci-dessous

Il existes des solutions tout-en-un sur le marché des logiciels libres ou non tels que [OpenMediaVault](https://www.openmediavault.org/), [TrueNas Scale](https://www.truenas.com/truenas-scale/), [Unraid](https://unraid.net/fr) qui s'intègrent tous pour créer ce genre de serveur. Bien qu'Unraid et OMV puissent répondre à mes besoins de stockage en vrac, ils s'intègrent également avec certains compromis. À défaut, TrueNas Scale s'oriente surtout sur la gestion d'un pool de disques durs en ZFS, et on sait tous que ZFS n'est pas très souple au niveau de l'évolutivité des disques.

L'un des principaux moteurs de la création de ce NAS est la facilité de gestion de l'espace disque disponible. Je voulais être en mesure d'étendre ce dernier assez facilement sans mettre la main à la poche en achetant mes disques durs directement au montage et pouvoir étaler ces achats au fur et à mesure de la montée en charge du serveur.

Je voulais, également, pouvoir être en mesure d'ajouter un seul disque et de l'ajouter à la capacité du pool de stockage tout en conservant une certaine redondance avec un minimum de configuration et de temps d'arrêt.


<div class="grid cards" markdown>

-   :material-server:{ .lg .middle } **Matériel & méthodologie**

    ---

    - Voici la [liste du matériel](/tech-stack/hardware/) que j'ai acquis pour ce projet.
    - La [méthodologie](/tech-stack/processus/) qui va nous permettre de construire ce NAS.

    [:octicons-arrow-right-24: En savoir plus](/tech-stack/hardware/)

-   :material-floppy:{ .lg .middle } **Guide d'installation**

    ---

    Guides pour l'installation de Proxmox Virtual Environment et de l'ensemble des outils du serveur.

    [:octicons-arrow-right-24: Débuter l'installation](/installation/proxmox/)

-   :material-folder-open:{ .lg .middle } **Post-installation**

    ---

    Le vrai coeur du système est ici !

    [:octicons-arrow-right-24: Lire la documentation](/post-install/)

-   :material-docker:{ .lg .middle } **Services**

    ---

    Créons l'ensemble de nos services !

    [:octicons-arrow-right-24: Explorez l'ensemble des services](/services/)

</div>