---
title: SnapRAID
icon: material/backup-restore
---

# **SnapRAID**

**SnapRAID** est un programme de sauvegarde conçu pour les baies de disques, stockant les informations de parité pour la récupération des données en cas de panne de disque maximum.

Principalement destiné aux centres multimédia domestiques avec des fichiers volumineux et rarement modifiés, SnapRAID offre plusieurs fonctionnalités :

- Vous pouvez utiliser des disques déjà remplis de fichiers sans avoir besoin de les reformater, en y accédant comme d'habitude,
- Toutes vos données sont hachées pour garantir leur intégrité et éviter toute corruption silencieuse,
- Lorsque le nombre de disques défaillants dépasse le nombre de parité, la perte de données se limite aux disques concernés ; les données sur d'autres disques restent accessibles,
- Si vous supprimez accidentellement des fichiers sur un disque, la récupération est possible,
- Les disques peuvent avoir différentes tailles,
- Vous pouvez ajouter des disques à tout moment,
- SnapRAID ne verrouille pas vos données ; vous pouvez arrêter de l'utiliser à tout moment sans reformater ni déplacer de données,
- Pour accéder à un fichier, un seul disque doit tourner, ce qui permet d'économiser de l'énergie et de réduire le bruit.


## Installation

 SnapRAID ne fournit pas de packages, nous devons donc le compiler nous-mêmes à partir des sources.

 # ces étapes supposent une installation Docker valide et fonctionnelle
``` shell
wget https://github.com/amadvance/snapraid/releases/download/v12.3/snapraid-12.3.tar.gz
tar xvf snapraid-*.tar.gz
cd snapraid-*
./configure
make
make check
sudo make install
```

Vérifiez l'installation réussie avec :
``` shell
root@homelab:/# snapraid --version
```

``` shell
$ root@homelab:/# snapraid smart

SnapRAID SMART report:

   Temp  Power   Error   FP Size
      C OnDays   Count        TB  Serial               Device    Disk
 -----------------------------------------------------------------------
      -      -       -    -  0.0  VBc37019e7-f48bd93f  /dev/sdd  -
      -      -       -  n/a    -  -                    /dev/sde  -
      -      -       -    -  0.0  VB142e38f1-07018a97  /dev/sdb  -
      -      -       -    -  0.0  VB0e6c06d6-e8cfeb66  /dev/sdf  -
      -      -       -    -  0.0  VB8156f647-895029ce  /dev/sdc  -
      -      -       -    -  0.0  VBd585156f-7c670e40  /dev/sda  -

The FP column is the estimated probability (in percentage) that the disk
is going to fail in the next year.

Probability that at least one disk is going to fail in the next year is 0%.
```

## Configuration

réation du fichier de configuration `/etc/snapraid.conf` :
``` shell
# PRÉREQUIS
root@homelab:/# mkdir /var/snapraid

# CONFIGURATION
root@homelab:/# vi /etc/snapraid.conf

# SnapRAID configuration file
# Parity location(s)
1-parity /mnt/parity/snapraid.parity

# Content file location(s)
content /var/snapraid.content
content /mnt/disk1/.snapraid.content
content /mnt/disk2/.snapraid.content

# Data disks
data d1 /mnt/disk1
data d2 /mnt/disk2

# Excludes hidden files and directories
exclude *.unrecoverable
exclude /tmp/
exclude /lost+found/
exclude downloads/
exclude appdata/
exclude *.!sync
exclude .AppleDouble
exclude ._AppleDouble
exclude .DS_Store
exclude ._.DS_Store
exclude .Thumbs.db
exclude .fseventsd
exclude .Spotlight-V100
exclude .TemporaryItems
exclude .Trashes
exclude .AppleDB
exclude .nfo


root@homelab:/# snapraid status
Self test...
Loading state from /var/snapraid/.snapraid.content...
WARNING! Content file '/var/snapraid/.snapraid.content' not found, attempting with another copy...
Loading state from /mnt/disk1/.snapraid.content...
WARNING! Content file '/mnt/disk1/.snapraid.content' not found, attempting with another copy...
Loading state from /mnt/disk2/.snapraid.content...
No content file found. Assuming empty.
Using 0 MiB of memory for the file-system.
SnapRAID status report:

   Files Fragmented Excess  Wasted  Used    Free  Use Name
            Files  Fragments  GB      GB      GB
       0       0       0     0.0       0       -   -  d1
       0       0       0     0.0       0       -   -  d2
 --------------------------------------------------------------------------
       0       0       0     0.0       0       0   0%

WARNING! Free space info will be valid after the first sync.
The array is empty.
```

À ce stade, on est prêt à lancer la commande `snapraid sync` pour créer les informations de parité :
``` shell
root@homelab:/# snapraid sync

Self test...
Loading state from /var/snapraid/.snapraid.content...
WARNING! Content file '/var/snapraid/.snapraid.content' not found, attempting with another copy...
Loading state from /mnt/disk1/.snapraid.content...
WARNING! Content file '/mnt/disk1/.snapraid.content' not found, attempting with another copy...
Loading state from /mnt/disk2/.snapraid.content...
No content file found. Assuming empty.
Scanning...
Scanned d1 in 0 seconds
Scanned d2 in 0 seconds
Using 0 MiB of memory for the file-system.
Initializing...
Resizing...
Saving state to /var/snapraid/.snapraid.content...
Saving state to /mnt/disk1/.snapraid.content...
Saving state to /mnt/disk2/.snapraid.content...
Verifying...
Verified /var/snapraid/.snapraid.content in 0 seconds
Verified /mnt/disk1/.snapraid.content in 0 seconds
Verified /mnt/disk2/.snapraid.content in 0 seconds
Nothing to do
```

## Automatisation du calcul de la parité

Comme SnapRAID est conçu pour fonctionner en prenant des instantanés, nous devons les configurer pour qu'ils soient calculés à intervalles réguliers. Nous pourrions simplement créer une tâche cron très simple et exécuter la synchronisation Snapraid dans le cadre de ce processus, mais il y a quelques situations dans lesquelles nous voulons un peu plus d'intelligence qu'un simple cron.

Pour ce faire, nous allons utiliser [snapraid-runner](https://github.com/Chronial/snapraid-runner) qui est un utilitaire fiable pour ajouter des portes logiques à l'exécution de SnapRAID.

[Ssnapraid-runner](https://github.com/Chronial/snapraid-runner) exécute Snapraid et envoie sa sortie à la console, à un fichier journal et par e-mail. Tout cela est configurable. Il peut être exécuté manuellement, mais son objectif principal est d'être exécuté via le planificateur cronjob de Linux ou encore Windows.

Pour installer, on commence par cloner le dépôt git :

``` shell
root@homelab:/# git clone https://github.com/Chronial/snapraid-runner.git /opt/snapraid-runner
```

Ensuite, on s'assure qu'on a bien configuré le fichier de configuration de Snapraid :

``` shell
root@homelab:/# cd /opt/snapraid-runner/
root@homelab:/opt/snapraid-runner# mv /opt/snapraid-runner/snapraid-runner.conf.example /opt/snapraid-runner/snapraid-runner.conf
root@homelab:/opt/snapraid-runner# vi snapraid-runner.conf

[snapraid]
; path to the snapraid executable (e.g. /bin/snapraid)
executable = /usr/local/bin/snapraid
; path to the snapraid config to be used
config = /etc/snapraid.conf
; abort operation if there are more deletes than this, set to -1 to disable
deletethreshold = 40
; if you want touch to be ran each time
touch = true

[logging]
; logfile to write to, leave empty to disable
file = snapraid.log
; maximum logfile size in KiB, leave empty for infinite
maxsize = 5000

[email]
; when to send an email, comma-separated list of [success, error]
sendon = success,error
; set to false to get full programm output via email
short = true
subject = [SnapRAID] Status Report:
from =
to =
; maximum email size in KiB
maxsize = 500

[smtp]
host = smtp.gmail.com
port = 587
ssl = false
tls = true
user = me@gmail.com
# generate an app specific password : https://support.google.com/accounts/answer/185833?hl=en 
password = password

[scrub]
; set to true to run scrub after sync
enabled = true
; scrub plan - either a percentage or one of [bad, new, full]
plan = 12
; minimum block age (in days) for scrubbing. Only used with percentage plans
older-than = 10
```

Modifiez le fichier de configuration pour snapraid-runner, un fichier par défaut est fourni dans `/opt/snapraid-runner/snapraid-runner.conf.example`.

Les paramètres suivants sont les plus intéressants lors de la configuration de ce fichier :

- `config = /etc/snapraid.conf` - Assurez-vous que cela indique l'endroit où votre fichier `snapraid.conf` est stocké,
- `deletethreshold = 40` - abandonner l'opération s'il y a plus de suppressions que cela, définir sur -1 pour désactiver,
- `touch = True` - Cela améliore la capacité de SnapRAID à reconnaître les fichiers déplacés et copiés, car cela rend l'horodatage presque unique, supprimant ainsi les doublons possibles,
- `[email]` - Si vous utilisez Gmail, vous devrez générer [un mot de passe spécifique à l'application](https://support.google.com/accounts/answer/185833?hl=en)
- `[scrub]` - Configurer les fonctionnalités de vérification périodique des données :
    - `enabled = True`
    - `plan = 12` - Le % du tableau à nettoyer,
    - `older-than = 10` - Nettoyer les données uniquement si elles datent de plus de ce nombre de jours.

Enfin, on crée une tâche cron pour exécuter automatiquement `snapraid-runner`. On doit s'assurer que les fichiers pour lesquels SnapRAID vérifie la parité ne changent pas pendant cette période. Idéalement, vers 4 ou 5 heures du matin, ce serait également une bonne idée de désactiver temporairement tous les services qui écrivent sur votre stockage pendant cette période - cela est cependant facultatif.

``` shell
root@homelab:/# crontab -e

00 01 * * * python3 /opt/snapraid-runner/snapraid-runner.py -c /opt/snapraid-runner/snapraid-runner.conf && curl -fsS --retry 3 https://hc-ping.com/123-1103-xyz-abc-123 > /dev/null
```

Lors d'une synchronisation, SnapRAID écrira un fichier .content dans `/var/snapraid` et nécessitera donc un accès en écriture à ce répertoire. L'exécution via sudo ou en tant que root est ici une solution simple et fiable.

Avec cron, c'est une bonne idée d'être aussi explicite que possible en ce qui concerne les chemins de fichiers. Ne vous fiez jamais aux chemins relatifs ou à la variable `PATH`. Peut-être avez-vous également remarqué qu'un contrôle de santé est configuré sur hc-ping.com.

## Cas d'utilisation

### Suppression de dossiers/fichiers

Cela vous donnera le chemin absolu du point de vue du système de fichiers : 

``` shell
snapraid diff --test-fmt path
```

Cela vous permettra de connaître le disque de données du point de vue du tableau `Snapraid` :

``` shell
snapraid diff --test-fmt disk
```

`Snapraid` ne s'attend pas à ce que vous fournissiez le chemin absolu du système de fichiers lors de la réparation des fichiers.


Cela recréera tout fichier supprimé dans l'ensemble du tableau :

``` shell
snapraid fix -m -v
```

Cela recréera tout fichier supprimé sur le disque de données d1 :

``` shell
snapraid fix -m -d d1
```


Cela recréera le contenu de n'importe quel dossier nommé `sample` n'importe où dans le tableau :  
``` shell
snapraid fix -m -f sample/
```


Si vous n'êtes pas sûr de ce qui va se passer, vous pouvez remplacer fix par check ci-dessus et ajouter -v comme ceci :
``` shell
snapraid check -m -f Example/ -v
```

`Snapraid` vous dira alors qu'il y a un problème avec chacun des fichiers correspondant au filtre ou qu'ils sont ok.