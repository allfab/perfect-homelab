# Activer Wake On LAN sur Debian

## Installation d'`ethtool`

``` shell
apt install ethtool
```

##  Vérification de l'interface

``` shell
ip a
```
Ou encore :
``` shell
vi /etc/network/interface
```

## Sélectionnez l'interface que vous souhaitez utiliser

Par example :
``` shell
enp2s0
```

## Activer le démarrage/réveil sur LAN temporairement

``` shell
sudo ethtool --change enp2s0 wol g
```

## Vérifiez l'état WOL de l'interface avec la commande suivante

``` shell
sudo ethtool enp2s0
```

## Retrouvez la section ci-dessous

``` shell
Wake-on: g
```

# Rendre le réveil sur le réseau local permanent

## Trouver le répertoire `ethtool`

``` shell
which ethtool
```

Sur Proxmox : /usr/sbin/ethtool

## Créez le fichier wol.service dans /etc/systemd/system/ avec le contenu suivant

``` shell
[Unit]
Description=Enable Wake On Lan

[Service]
Type=oneshot
ExecStart = /usr/sbin/ethtool --change enp2s0 wol g

[Install]
WantedBy=basic.target
```

## Activer le service

``` shell
sudo systemctl daemon-reload
sudo systemctl enable wol.service
```
