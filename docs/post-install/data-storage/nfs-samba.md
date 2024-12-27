---
title: NFS/Samba
icon: material/folder-network
---

# **NFS**

# **Configurer un partage Samba**

## Installation de Samba
``` bash
sudo apt install samba smbclient cifs-utils
```

### Définir les paramètres globaux de Samba

Editer le fichier **_/etc/samba/smb.conf_** :

```
[public]
	comment = Public Folder 
	path = /public 
	writable = yes 
	guest ok = yes 
	guest only = yes 
	force create mode = 775 
	orce directory mode = 775 

[private]
	comment = Private Folder
	path = /private
	writable = yes
	guest ok = no
	valid users = @smbshare
	force create mode = 770
	force directory mode = 770
	inherit permissions = yes

[NUC-Home]
   comment = NUC - Home
   path = /home/docker
   guest ok = no
   read only = no
   browseable = yes
   writeable = yes
   valid users = docker
```

## Créer un utilisateur et un groupe Samba

Nous avons besoin du groupe d'utilisateurs de partage Samba pour accéder au partage privé comme spécifié dans la configuration ci-dessus. Nous allons donc créer le groupe comme ci-dessous.

``` bash
sudo groupadd smbshare
```

Ajoutez les autorisations nécessaires pour le partage privé.

``` bash
sudo chgrp -R smbshare /private/
sudo chgrp -R smbshare /public
```

Définissez les bonnes autorisations de répertoire.
``` bash
sudo chmod 2770 /private/
sudo chmod 2775 /public
```

Dans la commande ci-dessus, la valeur 2 au début correspond au stickybit. Cela permet aux fichiers nouvellement créés d'hériter du groupe parent.

Ensuite, créez un utilisateur local sans connexion pour accéder au partage privé :
``` bash
sudo useradd -M -s /sbin/nologin sambauser
```

Ajoutez l'utilisateur au groupe de partage Samba créé ci-dessus :
``` bash
sudo usermod -aG smbshare sambauser
```

Créez maintenant un mot de passe SMB pour l'utilisateur :
``` bash
sudo smbpasswd -a sambauser```

Activez le compte créé :
``` bash
sudo smbpasswd -e sambauser
```

## Vérifiez la configuration de Samba

Une fois les modifications apportées au fichier de configuration, il est recommandé de le tester à l'aide de la commande ci-dessous :
``` bash
sudo testparm
```

Si vous n'avez pas de message d'erreur, on redémarre le service :
``` bash
sudo systemctl restart nmbd
```

## Accéder aux partages depuis un client

Ce guide montre comment accéder aux fichiers partagés à l'aide des systèmes Windows et Linux. Tout d’abord, essayez d’accéder au partage depuis votre ordinateur local.

``` bash
smbclient '\\localhost\private' -U sambauser
```