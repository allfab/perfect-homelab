# **BIENVENUE SUR PERFECT NAS SERVER**
![logo-light](assets/images/overview/logo.png#only-light)
![logo-dark](assets/images/overview/logo-dark.png#only-dark)

## **:material-chat-question: Pourquoi ce site ?**

Cela fait longtemps que je désire m'équiper d'un **NAS**. Après avoir longtemps tergiversé sur la question de m'équiper d'un NAS du commerce (Synology, QNAP, etc) et sur quel modèle choisir, j'ai opté pour un **Homelab** à assembler, installer et configurer soi-même. 

Le fameux **Do It Yourself** de A à Z ! 

Étant passionné d'informatique, je souhaitais créer mom propre laboratoire domestique pour tester de nouveaux logiciels, serveurs et configurations de réseau. J'ai donc dans un premier temps recyclé mon vieux **Rasperry PI 3**. Je me suis vite aperçu qu'avec la facilité de déploiement de stacks Docker (oui, j'utilise cete techno merveilleuse !) que ce dernier devenait bien trop vite juste en terme de ressources.

Je me suis orienté sur l'acquisition d'**un mini-PC [Intel® NUC NUC7i3BNH](https://www.intel.fr/content/www/fr/fr/products/sku/95066/intel-nuc-kit-nuc7i3bnh/specifications.html)** d'occasion que j'ai gonflé, après quelques mois d'utilisation, avec 32GO de RAM (il n'y en avait que 8GO à l'origine).


## **:material-asterisk: Pour quel usage, me direz-vous ?**

C'était surtout pour m'amuser avec la domotique et avec **[Home Assistant](https://www.home-assistant.io/)** mais on se rend assez vite compte qu'il est tellement facile de mettre en oeuvre des services autohébergés via **[Proxmox](https://www.proxmox.com/en/)** ou encore **[Docker](https://www.docker.com/)** que l'usage premier est rapidement détourné et qu'on veut en faire toujours plus.

!!! note
    Si vous souhaitez créer un serveur NAS et vous en servir pour faire tourner d'autres services, vous êtes au bon endroit !

Ce site documente les nombreux aspects de la création d'un serveur NAS ***Network Attached Storage*** à l'aide de logiciels libres et open source. Ce serveur me servira d'**Homelab**.


## **:material-checkbox-marked: Quelques formalités**

L'acronyme NAS, ***pour Network Attached Storage***, désigne un périphérique de stockage utilisé pour le stockage et le partage de fichiers via un réseau (Ethernet, la plupart du temps). Il s'agit d'un serveur de fichiers capable de fonctionner de façon autonome. On le résume parfois à un disque dur relié à un réseau (privé, professionnel, etc.). Il peut être traduit en français par serveur de stockage en réseau, ou stockage raccordé en réseau. Documents, images ou vidéos peuvent être servis depuis un NAS sur les terminaux connectés à son réseau. Des NAS peuvent également être utilisés comme un serveur web.

Souvent comparé aux services de stockage similaires comme Dropbox, Drive, etc, le NAS présente l'avantage de privatiser le réseau utilisé pour le stockage des fichiers. Il autorise un volume de stockage très important et un partage sur plusieurs appareil.

<figure markdown>
  ![NAS](assets/images/overview/thomas-jensen-ISG-rUel0Uw-unsplash.jpg){: width=660 }
  <figcaption><i>Photo de Thomas Jensen sur unsplash.com</i></figcaption>
</figure>


!!! note "OBJECTIF"
    Installer et configurer l'ensemble des briques matériels et logicielles qui feront tourner notre NAS

## **:material-lightbulb-on: Prérequis**

De nombreux aspects de ce tutoriel sont basés sur mon expérience et celle d'autres amateurs/professionnels qui ont déjà tenté de constituer ce genre de serveur. Ce site est à destination d'un public averti.

Il est nécessaire de possèder quelques connaissances basiques :

 - Test
 - Test


## **L'envers du décors**

Un serveur de virtualisation avec un stockage groupé extensible. Qui ne voudrait pas ça ?

- **Hyperviseur** : Proxmox
- **Pool de stockage** : MergerFS et NFS
- **Sauvegarde** : SnapRAID
- **Partage** : NFS

Mon objectif est de créer un serveur pour héberger mes services qui pourrait continuer à croître avec mes besoins au fil du temps.

### **:simple-proxmox: Hyperviseur**

Un hyperviseur comme système d'exploitation de base me permet de séparer mes services pour plus de stabilité, d'ajouter/supprimer des serveurs et d'utiliser plus facilement des outils d'automatisation comme Ansible pour gérer l'infrastructure de mon laboratoire. 

J'ai choisi **Proxmox** car il était open source, stable et permettait la création à la fois de VM et de conteneurs.

### **Stockage et sauvegarde mutualisés**

Je voulais prendre un tas de gros disques, les faire fonctionner ensemble comme un seul, accessible depuis chaque VM et disposer d'une configuration de sauvegarde de type RAID.

J'avais initialement prévu d'utiliser zfs mais j'ai trouvé que cela ne répondait pas tout à fait à mes besoins. zfs a du mal à gérer des disques de différentes tailles ainsi que des disques de grande taille, rend difficile l'ajout de disques supplémentaires à un pool et, bien qu'open source, est publié sous une licence non GNU par Oracle. Au final, un mélange de MergerFS, SnapRAID et NFS était exactement ce que je recherchais.

### **MergerFS**

MergerFS est un « système de fichiers Union » qui regroupe plusieurs lecteurs en un seul lecteur virtuel.

### **NFS**

NFS me permet de rendre le pool MergerFS accessible à chaque machine virtuelle.

### **SnapRAID**

SnapRAID est un programme de sauvegarde pour les baies de disques. Les sauvegardes de matrice de disques fonctionnent en utilisant un lecteur de parité pour stocker les informations des autres lecteurs de sorte que si l'un d'eux meurt, la récupération est possible en utilisant les informations de la parité. SnapRAID ne fonctionne pas au niveau matériel comme le RAID, mais au niveau logiciel et ne synchronise la parité que lorsque cela est demandé.

## **Constats**

- MergerFS n'a aucun problème avec les disques volumineux et de tailles différentes.
- L'ajout de nouveaux lecteurs au pool MergerFS est aussi simple que l'ajout d'une ligne au fichier de configuration.
- Chaque lecteur contient toujours les données dans un format lisible puisque les lecteurs sont regroupés au niveau logiciel. Cela signifie que vous pouvez supprimer un disque de la baie sans affecter la baie et toujours pouvoir accéder aux données du disque.
- SnapRAID fournit une solution de sauvegarde flexible pour récupérer les disques défaillants lors de la dernière synchronisation.

## **:fontawesome-solid-feather-pointed: À propos de l'auteur**

![Allfab](assets/images/overview/allfab.jpeg){: align=right width=180 }
Cette documentation est écrite et maintenue par [Fabien ALLAMANCHE](https://www.linkedin.com/in/fabien-allamanche/) (alias [@allfab](https://mapstodon.space/@allfab)). 
