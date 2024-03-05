---
title: Matériel
icon: material/harddisk
---

# Matériel

=== "Décembre 2023"

    ## Décembre 2023

    ### Composants

    Voici un aperçu du matériel mise en oeuvre :

    | Composant    | Modèle                                                                                 | Pour quelle raison ?                                                                           |
    | ------------ | -------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
    | Boîtier      | [Fractal Design Node 304 Noir ](https://www.ldlc.com/fiche/PB00135558.html)            | Recommandation via GuiPom                                                                      |
    | Carte-mère   | [ASRock H670M-ITX/ax](https://www.asrock.com/mb/Intel/H670M-ITXax/index.asp)           | 4 SATA3 - 1 Hyper M.2 (PCIe Gen4 x4) - 1 Hyper M.2 (PCIe Gen4 x4 & SATA3)                      |
    | CPU          | [Intel Core i3-13100 (3.4 GHz / 4.5 GHz) ](https://www.ldlc.com/fiche/PB00536091.html) | iGPU avec transcodage Quicksync intégré                                                        |
    | Mémoire      | [Crucial Pro 64GB Kit (2x32GB) DDR4-3200 - Non ECC](https://www.crucial.fr/memory/DDR4/CP2K32G4DFRA32A)| Suffisant pour exécuter l’intégralité de ma pile de production                 |
    | SSD boot     | [2 * Crucial P5 Plus 1To NVMe SSD](https://amzn.eu/d/8PFIjH0)                              | Disque SSD système                                                                             |
    | Alimentation | [Be Quiet SYSTEM POWER 9 400W](https://www.bequiet.com/fr/powersupply/1281)            |                                                                                                |
    | Disques dur  | Un mélange de disques de 1 à 18 To                                                     |                                                                                                |

    
    ### Logiciels / Conteneurs

    | Type              | Produit / Version                                          | Pour quelle raison ?                                                                                                    |
    | ----------------- | ------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------- |
    | OS                | [Proxmox 8.1](https://www.proxmox.com/en/proxmox-virtual-environment/overview) | Il y a d'autres produits opensource plus performants que Proxmox pour gérer ses VMs ?               |
    | Parité            | [SnapRAID](http://www.snapraid.it/)                                            | Stocke les informations de parité de vos données et récupère jusqu'à six pannes de disque           |
    | Gestion des HDDs  | [mergerfs](https://github.com/trapexit/mergerfsd)                              | Système de fichiers destiné à simplifier le stockage et la gestion des fichiers sur de nombreux HDD |
    | Conteneurs        | [docker](https://www.docker.com/)                                              | Existe-t-il une autre façon d’exécuter un logiciel ?                                                |

    | Conteneur                                        | Usage                                    | Lien de contenu pertinent                                         |
    | ------------------------------------------------ | ------------------------------------------ | ---------------------------------------------------------------- |
    | [Traefik](https://traefik.io/)                   | Reverse proxy                              |             |


=== "Avant"
    
    ## Avant

    ### Composants

    | Composant     | Modèle                                                                                 | Pour quelle raison ?                                                                                |
    | ------------- | -------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------- |
    | Intel NUC     | [Intel® NUC NUC7i3BNH](https://www.intel.fr/content/www/fr/fr/products/sku/95066/intel-nuc-kit-nuc7i3bnh/specifications.html) | Faible consommation électrique - Facile à trouver d'occasion |
    | Raspberri PI 3| [Raspberry Pi 3 Model B](https://www.raspberrypi.com/products/raspberry-pi-3-model-b/) | Ordinateur monocarte avec connectivité LAN sans fil et Bluetooth                                    |

    ### Logiciels / Conteneurs

    | Type              | Produit / Version                                          | Pour quelle raison ?                                                                                                    |
    | ----------------- | ------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------- |
    | OS                | [Debian 11](https://www.debian.org/index.fr.html)                              | Parce que j'aime bien Debian                                                                        |
    | Parité            | -                                                                              |                                                                                                     |
    | Gestion des HDDs  | -                                                                              |                                                                                                     |
    | Conteneurs        | [docker](https://www.docker.com/)                                              | Existe-t-il une autre façon d’exécuter un logiciel ?                                                 |


    | Conteneur                                        | Usage                                    | Lien de contenu pertinent                                         |
    | ------------------------------------------------ | ------------------------------------------ | ---------------------------------------------------------------- |
    | [Swag](https://docs.linuxserver.io/general/swag/)| Reverse proxy                              |           |
    
