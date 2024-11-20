---
title: WIKI - Linux - Services
icon: material/ssh
tags:
    - Wiki
    - Linux
    - SSH
---

# WIKI - Linux - Services

# **Configuration - SSH**

## **Génération d’une nouvelle clé SSH**

Vous pouvez générer une nouvelle clé SSH sur votre ordinateur local. Après avoir généré la clé, vous pouvez ajouter la clé publique à votre serveur.

**Génération via l'outil ssh-keygen**

`ssh-keygen` fourni un prompt pour vous aider

!!! warning "ATTENTION"
    Ajouter une passphrase sinon une clef ssh est plus dangereuse qu'un password

Usage basique :
``` shell
ssh-keygen -t ecdsa -b 521
```

- `-t` : Type de clef / Algorithme utilisé (rsa, dsa, ecdsa),
- `-b` : Longeur de clef (dépend de l'algo: ici ecdsa 521).

En spécifiant la localisation de sortie :
``` shell
ssh-keygen -t ecdsa -b 521 -f /home/<user>/.ssh/maclefprivee
```

- `-f` : localisation de sortie.


## **Ajout de celle-ci à l'hôte distant via ssh-agent**


Ajout de la clé publique sur le host distant :
``` shell
vim /home/user/.ssh/authorized_keys
```

Via ssh-agent :
``` shell
ssh-copy-id -i /home/<user>/.ssh/maclefprivee user@monhost
```

Restreindre à une adresse IP :
``` shell
from="10.0.0.?,*.example.com",no-X11-forwarding ssh-rsa AB3Nz...EN8w== user@monhost
```

Utilisation de la clé :
``` shell
ssh -i /localisation/key/privee user@monhost
```

Configuration sur l'hôte hébergeant la clé privée :
``` yaml
touch ~/.ssh/config
chmod 600 ~/.ssh/config
cat ~/.ssh/config

Host * !monhost*
    User monuser
    Port 22
    IdentityFile /home/user/.ssh/maclefprivee
    LogLevel INFO
    Compression yes
    ForwardAgent yes
    ForwardX11 yes
```

Astuce pour bypasser la conf :
``` shell
ssh -F /dev/null user@monhosts
```

## **Supprimer la clé de known_hosts**

``` shell
ssh-keygen -R <hostname>
```