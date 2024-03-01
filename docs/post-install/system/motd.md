---
title: Post-Installation - MOTD
icon: material/message
---

# **Post-Installation - MOTD**

## Créer un **MOTD[^1]** personnalisé ou une bannière de connexion sous Linux

Autrefois, avant les interfaces graphiques, les administrateurs système laissaient un message aux utilisateurs en utilisant le fichier « MOTD » du message du jour.
L'option motd est toujours disponible sur la plupart des systèmes Linux modernes. Dans cet article, nous allons voir comment créer une jolie bannière de connexion personnalisée avec des illustrations ASCII et des informations système.

[^1]: Message of the Day - Message du jour


### Définition d'un MOTD de texte de base sous Linux

Vous pouvez facilement définir un message texte de base du jour en éditant le fichier /etc/motd. Tout texte que vous placez dans le fichier sera affiché lors de la connexion de n'importe quel utilisateur.

Exemple:

```bash
sudo vi /etc/motd
#######################################
# CECI EST UN MESSAGE DE TEST DANS /etc/motd #
#######################################
```

Désormais, lorsque quelqu'un se connectera via le terminal ou ssh, il verra le message.
```bash
ssh monadresseip
Mot de passe de user@monadresseip :
#######################################
# CECI EST UN MESSAGE DE TEST DANS /etc/motd #
#######################################
Dernière connexion : mardi 07 novembre 2023 11:44:58 à partir du 91.184.102.244
```

Cela devrait fonctionner sur n'importe quelle machine Unix ou Linux, quelle que soit la distribution.

### Script de connexion personnalisé dans le profil Etc

Une autre option, plus flexible, consiste à créer un script et à le placer dans le dossier /etc/profile.d/. Tout script de ce dossier s'exécutera lorsqu'un utilisateur se connectera. 

L'utilisation d'un script dans /etc/profile.d vous offre des possibilités presque illimitées. J'ai utilisé un générateur d'art ASCII pour créer le nom de mon serveur via [**figurine**](https://github.com/arsham/figurine) et j'ai créé cette bannière de connexion :
![image](../../assets/wiki/motd/1840185/9d8d7991-5caf-4c44-9db9-c6a32e9834d5)

J'ai ensuite ajouté la commande [**neofetch**](https://github.com/dylanaraps/neofetch) pour me montrer des informations sur le serveur.


## Utilisation de figurine pour créer l'entête de la bannière de connexion personnalisée

[**figurine**](https://github.com/arsham/figurine) permet d'imprimer un texte avec style sur un terminal :
![figurine-neofetch-motd](../../../../assets/images/wiki/motd/figurine-neofetch-motd.png)
![figurine-samples](../../../../assets/images/wiki/motd/figurine-samples.png)

### Installation

Vous pouvez télécharger le dernier binaire de **Figurine** [**ici**](https://github.com/arsham/figurine/releases) :

Ensuite :
```bash
tar xzf https://github.com/arsham/figurine/releases/download/v1.3.0/figurine_linux_amd64_v1.3.0.tar.gz
sudo chmod +x deploy/figurine
mv deploy/figurine /usr/local/bin
sudo rm -Rf deploy figurine_linux_amd64_v1.3.0.tar.gz
```

### Usage

Chaque fois que l'application est appelée, elle choisit une police aléatoire pour restituer le message. Passez le message que vous souhaitez décorer comme arguments.

```bash
figurine Some Text
```

Vous pouvez imprimer les polices disponibles :

```bash
figurine -l
figurine -l -s
figurine -ls Sample Text
```

Pour définir une police :

```bash
figurine -f "Poison.flf" Some Text
```

Pour obtenir une liste des arguments disponibles :

```bash
figurine -h
```


### Utilisation de neofetch pour enrichir la bannière de connexion personnalisée

L'utilitaire [**neofetch**](https://github.com/dylanaraps/neofetch) est un outil de ligne de commande qui affiche le logo de distribution et les informations système pour le système sur lequel il est installé.

Installation de l'utilitaire neofetch :

Vous pouvez installer **neofetch** facilement avec la plupart des gestionnaires de packages.

Pour installer neofetch sur Debian/Ubuntu :
```bash
sudo apt-get install neofetch
```

Utiliser neofetch :

Bien que **neofetch** ait une tonne d'options, il vous suffit de l'invoquer sans aucun argument pour obtenir un joli logo ascii et des informations système (comme vu ci-dessus) :

```bash
neofetch
```

Pour utiliser neofetch comme bannière de connexion, exécutez simplement la commande suivante pour créer un script qui s'exécute lors de la connexion.


### Création du script /etc/profile.d/motd.sh

```bash
sudo vi /etc/profile.d/motd.sh
```

Ajoutez ces lignes :
```bash
#!bin/bash

figurine -f "3d.flf" mon-serveur
neofetch
```

On change les droits :
```bash
sudo chmod +x /etc/profile.d/motd.sh
```

Par défaut, le script est exécuté à la connexion de l'utilisateur via SSH.