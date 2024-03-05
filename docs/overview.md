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

## :material-graph: **Méthodologie**

On va donc retrouver sous le capot :

- **Proxmox Virtual Environment** :
    - **Installation en baremetal**, [Proxmox Virtual Environment](https://www.proxmox.com/en/proxmox-virtual-environment/overview) est une plate-forme complète de gestion de serveurs open source pour la virtualisation d'entreprise. Il intègre étroitement l'hyperviseur KVM et les conteneurs Linux (LXC), la fonctionnalité de stockage et de réseau définie par logiciel, sur une plate-forme unique. Avec une interface utilisateur basée sur le web, vous pouvez gérer les machines virtuelles et les conteneurs, la haute disponibilité pour les clusters ou les outils de reprise après sinistre intégrés avec facilité. **Par conséquent, il y a une énorme marge de manœuvre pour l'évolutivité du serveur.**

!!! success "Proxmox est le bon choix pour vous si :"

    1. Vous aimez une distribution Linux stable qui est Debian,
    2. Vous voulez faire fonctionner des machines virtuelles et des conteneurs LXC,
    3. Vous voulez utiliser la technologie ZFS,
    4. Vous souhaitez regrouper plusieurs serveurs,
    5. Vous souhaitez aller plus loin avec [Proxmox Backup Server](https://www.proxmox.com/en/proxmox-backup-server/overview).


- **Pool de stockage** : [MergerFS](https://github.com/trapexit/mergerfs) 

    [MergerFS](https://github.com/trapexit/mergerfs) est un système de fichiers d'union très puissant. Il sera utilisé dans ce projet pour **« fusionner »** les disques ensemble pour créer un point de montage unifié de stockage. À noter que [MergerFS](https://github.com/trapexit/mergerfs) utilise des politiques de stockage pour définir comment les données sont copiées/hiérarchisées sur les différents disques qui composent le pool unifié, et ce sont ces politiques qui permettent la configuration que j'essaie d'atteindre. À savoir qu'une [politique](https://github.com/trapexit/mergerfs?tab=readme-ov-file#policies) est l'algorithme utilisé pour choisir une ou plusieurs branches (comprendre disques) sur lesquelles une fonction doit travailler ou, de manière générale, comment la fonction se comporte.
    
    Il en existe [une bonne liste](https://github.com/trapexit/mergerfs?tab=readme-ov-file#policies) mais je m'attarderais sur 2 qui ont retenu mon attention, `lfs` et `epmfs` :
    
    - `lfs` pour `Least Free Space` - ***choix de la branche avec le moins d’espace libre disponible*** : vise à équilibrer l'espace libre sur tous les disques. MergerFS écrit toujours de nouvelles données sur le disque avec l'espace le moins libre disponible.

    !!! abstract "Note"

        Bien que cela utilise uniformément tous les disques, cela représente des inconvénients importants pour des cas d'utilisation spécifiques. Pour les collections de médias, où les fichiers connexes (comme les épisodes d'une émission de télévision) sont souvent consultés ensemble, la politique  `lfs` peut diffuser ces fichiers à travers plusieurs disques. Par exemple, si vous avez quatre saisons d'émission télévisée, les épisodes de chaque saison pourraient se retrouver sur un disque différent.

        Cette approche peut conduire à :

        - une récupération inefficace : l'accès à une saison complète pourrait nécessiter la filature de plusieurs disques, plus de consommation d'énergie, l'usure des disques,
        - un cauchemare dans l'organisation des fichiers : l'étalement des fichiers connexes est lourd pour travailler sur les données qui devraient être ensemble,
        - Si on perd un lecteur et de la parité, il est plus facile de récupérer une petite partie de données similaires, contre des milliers de fichiers aléatoires.

    - `epmfs` pour `Existing Path, Most Free Space` - ***parmi toutes les branches sur lesquelles le chemin relatif existe, choisissez la branche avec le plus d'espace libre*** : priorité au répertoire existant, si un répertoire existe déjà sur l'un des disques, de nouveaux fichiers destinés à ce répertoire y seront placés. Cela garantit que les fichiers connexes, comme les épisodes de la même saison télévisée, restent ensemble sur le même disque tant qu'il y a de l'espace.

    !!! abstract "Note"

        Si le répertoire n'existe sur aucun disque (par exemple, lors de l'ajout d'une nouvelle émission de télévision ou d'une nouvelle saison), l'annuaire sera créé sur le disque avec l'espace le plus important. Cela aide à équilibrer l'utilisation globale du stockage à travers les disques.

    La politique `epmfs` combine le meilleur des deux options. Il maintient les fichiers connexes ensemble pour plus de clarté, tout en veillant à ce que l'espace disque soit utilisé efficacement, et c'est donc ce que je vais utiliser sur mon pool de données MergerFS.


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