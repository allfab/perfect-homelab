# Activer Wake On LAN sur Debian

## Installation d'`ethtool`

``` shell
root@morpheus:~# apt install ethtool
```

##  Vérification de l'interface

``` shell
root@morpheus:~# ip a
```
Ou encore :
``` shell
root@morpheus:~# vi /etc/network/interface
```

## Sélectionnez l'interface que vous souhaitez utiliser

Par example :
``` shell
root@morpheus:~# enp2s0
```

## Activer le démarrage/réveil sur LAN temporairement

``` shell
root@morpheus:~# ethtool --change enp2s0 wol g
```

## Vérifiez l'état WOL de l'interface avec la commande suivante

``` shell
root@morpheus:~# ethtool enp2s0
```

## Retrouvez la section ci-dessous `Wake-on: g`

``` shell
root@morpheus:~# ethtool enp2s0 
Settings for enp2s0:
        Supported ports: [ TP    MII ]
        Supported link modes:   10baseT/Half 10baseT/Full
                                100baseT/Half 100baseT/Full
                                1000baseT/Full
                                2500baseT/Full
        Supported pause frame use: Symmetric Receive-only
        Supports auto-negotiation: Yes
        Supported FEC modes: Not reported
        Advertised link modes:  10baseT/Half 10baseT/Full
                                100baseT/Half 100baseT/Full
                                1000baseT/Full
                                2500baseT/Full
        Advertised pause frame use: Symmetric Receive-only
        Advertised auto-negotiation: Yes
        Advertised FEC modes: Not reported
        Link partner advertised link modes:  10baseT/Half 10baseT/Full
                                             100baseT/Half 100baseT/Full
                                             1000baseT/Half 1000baseT/Full
        Link partner advertised pause frame use: Symmetric Receive-only
        Link partner advertised auto-negotiation: Yes
        Link partner advertised FEC modes: Not reported
        Speed: 1000Mb/s
        Duplex: Full
        Auto-negotiation: on
        master-slave cfg: preferred slave
        master-slave status: master
        Port: Twisted Pair
        PHYAD: 0
        Transceiver: external
        MDI-X: Unknown
        Supports Wake-on: pumbg
        Wake-on: g
        Link detected: yes
```

# Rendre le réveil sur le réseau local permanent

## Trouver le répertoire `ethtool`

``` shell
root@morpheus:~# which ethtool
```

Sur Proxmox : `/usr/sbin/ethtool`

## Créez le fichier wol.service dans /etc/systemd/system/ avec le contenu suivant

``` shell
root@morpheus:~# vi /etc/systemd/system/wol.service
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
root@morpheus:~# systemctl daemon-reload
root@morpheus:~# systemctl enable wol.service
```
