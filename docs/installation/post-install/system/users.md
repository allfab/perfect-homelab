---
title: Post-Installation - Utilisateurs
icon: material/account
---

# **Post-Installation - Utilisateur(s)**

## Créer un **utilisateur** standard

`useradd` est un utilitaire de ligne de commande qui peut être utilisé pour créer de nouveaux utilisateurs sur les systèmes Linux et Unix.

``` shell
useradd [OPTIONS] <username>
```

Sur la plupart des distributions Linux, lors de la création d'un nouveau compte utilisateur avec useradd, le répertoire personnel de l'utilisateur n'est pas créé. Il faut utiliser l'option `-m` ou  `--create-home` pour créer le répertoire de base de l'utilisateur sous `/home/<username>`.

Cette commande crée le répertoire personnel du nouvel utilisateur et copie les fichiers du répertoire /etc/skel vers le répertoire personnel de l'utilisateur. Si vous listez les fichiers dans le répertoire `/home/<username>`, vous verrez les fichiers d'initialisation via :

``` shell
ls -la /home/<username>/
```


Lorsqu'un nouvel utilisateur est créé, son shell de connexion est défini sur celui spécifié dans le fichier `/etc/default/useradd`. Dans certaines distributions, le shell par défaut est défini sur `/bin/sh`, tandis que dans d'autres, il est défini sur `/bin/bash`.

L'option `-s` ou `--shell` vous permet de spécifier le shell de connexion du nouvel utilisateur.

Voici un exemple montrant comment créer un nouvel utilisateur nommé john avec `/usr/bin/zsh` comme type de shell de connexion :

Créons notre utilisateur :

``` shell
useradd -m -s /bin/bash john
```

L'utilisateur a la possibilité d'écrire, de modifier et de supprimer des fichiers et des répertoires dans le répertoire personnel.

## Mot de passe : **passwd**

Assignons maintenant un mot de passe à cet utilisateur :

``` shell
passwd <username>
```

Tapez le mot de passe 2 fois.