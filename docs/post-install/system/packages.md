---
title: Post-Installation - Utilitaires indispensables
icon: material/package
---

# **Post-Installation - Utilitaires indispensables**

## Paquets dans les dépôts

``` shell
root@homelab:~#   apt install bash-completion \
	build-essential \
	curl \
	dnsutils \
	git \
	htop \
	ffmpeg \
	iftop \
	intel-gpu-tools \
	iotop \
	ipmitool \
	lm-sensors \
	mc \
	molly-guard \
	ncdu \
	net-tools \
	nfs-kernel-server \
	nmap \
	nvme-cli \
	openssh-server \
	python3 \
	qemu-guest-agent \
	sanoid \
	ssh-import-id \
	smartmontools \
	sudo \
	tree \
	vim \
	wget \
	zfsutils-linux
```

- `bash-completion` : complétions programmables pour l'interpréteur bash,
- `build-essential` : liste informative des paquets de construction essentiels,
- `curl` : outil en ligne de commande pour transférer des données avec une syntaxe URL,
- `dnsutils` : Package de transition pour bind9-dnsutils - clients fournis par BIND 9,
- `git` : système de gestion de versions distribué, rapide et évolutif,
- `htop` : outil interactif de visualisation de processus,
- `ffmpeg` : outils pour transcoder, diffuser en flux continu, et lire les fichiers multimédia,
- `iftop` : affichage d'informations sur l'utilisation de la bande passante d'une interface réseau,
- `intel-gpu-tools` : Outils de débogage des pilotes graphiques Intel,
- `iotop` : moniteur simple des E/S dans le style de top,
- `ipmitool` : utilitaire de contrôle IPMI avec pilote noyau ou interface réseau,
- `lm-sensors` : utilitaires pour lire les capteurs de température, tension et ventilateur,
- `mc` : Midnight Commander - gestionnaire de fichiers évolué,
- `molly-guard` : protection de machines d’arrêts et de redémarrages accidentels,
- `mutt` : outil de lecture de courriel en mode texte, gérant MIME, GPG, PGP et les fils de discussion,
- `ncdu` : visualiseur d'utilisation de disque avec ncurses,
- `net-tools` : boîte à outils NET-3 pour le réseau,
- `nfs-kernel-server` : gestion du serveur NFS du noyau,
- `nmap` : Cartographe de réseau,
- `nvme-cli` : NVMe management tool,
- `openssh-server` : serveur shell sécurisé (SSH), pour accèder à des machines à distance,
- `python3` : langage orienté objet interactif de haut niveau,
- `python-setuptools` : améliorations de Python Distutils,
- `qemu-guest-agent` : Agent système qemu côté invité,
- `sanoid` : outil de gestion et de réplication d'instantanés ZFS basé sur des règles,
- `screen` : multiplexeur d'écran avec une émulation de terminal VT100/ANSI,
- `ssh-import-id` : récupérer en toute sécurité une clé publique SSH et l'installer localement,
- `smartmontools` : contrôle et surveillance de systèmes de stockage utilisant S.M.A.R.T.,
- `sudo` : fournit des privilèges de super-utilisateurs à des clients spécifiques,
- `tmux` : multiplexeur de terminal,
- `tree` : affichage d’un arbre indenté de répertoires, en couleur,
- `vim` : éditeur vi amélioré,
- `wget` : récupération de fichiers sur le réseau,
- `wireguard-tools` : tunnel VPN du noyau rapide, moderne et sécurisé (utilitaires de l'espace utilisateur),
- `zfsutils-linux` : outils de ligne de commande pour gérer les systèmes de fichiers OpenZFS.


## DUF - Disk Usage/Free Utility

Référence : [https://github.com/muesli/duf](https://github.com/muesli/duf)

### Installation

Next

### Usage

You can simply start duf without any command-line arguments:

duf

If you supply arguments, duf will only list specific devices & mount points:

duf /home /some/file

If you want to list everything (including pseudo, duplicate, inaccessible file systems):

duf --all

Filtering

You can show and hide specific tables:

duf --only local,network,fuse,special,loops,binds
duf --hide local,network,fuse,special,loops,binds

You can also show and hide specific filesystems:

duf --only-fs tmpfs,vfat
duf --hide-fs tmpfs,vfat

...or specific mount points:

duf --only-mp /,/home,/dev
duf --hide-mp /,/home,/dev

Wildcards inside quotes work:

duf --only-mp '/sys/*,/dev/*'

Display options

Sort the output:

duf --sort size

Valid keys are: mountpoint, size, used, avail, usage, inodes, inodes_used, inodes_avail, inodes_usage, type, filesystem.

Show or hide specific columns:

duf --output mountpoint,size,usage

Valid keys are: mountpoint, size, used, avail, usage, inodes, inodes_used, inodes_avail, inodes_usage, type, filesystem.

List inode information instead of block usage:

duf --inodes

If duf doesn't detect your terminal's colors correctly, you can set a theme:

duf --theme light

Color-coding & Thresholds

duf highlights the availability & usage columns in red, green, or yellow, depending on how much space is still available. You can set your own thresholds:

duf --avail-threshold="10G,1G"
duf --usage-threshold="0.5,0.9"

Bonus

If you prefer your output as JSON:

duf --json
