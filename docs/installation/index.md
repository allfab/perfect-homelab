---
title: Installation
# icon: material/server
---

# **Installation**

Un serveur de virtualisation avec un stockage groupé extensible. Qui ne voudrait pas ça ?

- **Hyperviseur** : Proxmox
- **Pool de stockage** : MergerFS et NFS
- **Sauvegarde** : SnapRAID
- **Partage** : NFS

## **L'envers du décors**

Mon objectif est de créer un serveur pour héberger mes services qui pourrait continuer à croître avec mes besoins au fil du temps.

### **Hyperviseur**

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