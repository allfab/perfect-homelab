---
title: WIKI - Docker - CLI
icon: material/console
---

# Docker CLI

Pour répertorier les commandes disponibles, exécutez `docker` sans paramètres ou exécutez `docker help` :

Les [commandes](https://docs.docker.com/engine/reference/commandline/cli/) de base pour la CLI Docker.


## **Exécuter des conteneurs**

COMMAND | DESCRIPTION
---|---
`docker run IMAGE` | Démarrer un nouveau conteneur
`docker run --name CONTAINER IMAGE` | Démarrez un nouveau conteneur et définissez un nom
`docker run -p HOSTPORT:CONTAINERPORT IMAGE` | Démarrer un nouveau conteneur avec des ports mappés
`docker run -P IMAGE` | Démarrez un nouveau conteneur et mappez tous les ports

## **Gestion des conteneurs**

COMMAND | DESCRIPTION
---|---
`docker create IMAGE` | Créer un nouveau conteneur
`docker start CONTAINER` | Démarrer un conteneur
`docker stop CONTAINER` | Arrêt gracieux d'un conteneur
`docker kill CONTAINER` | Tuer (SIGKILL) un conteneur
`docker restart CONTAINER` | Arrêter et redémarrer gracieucesement un conteneur
`docker pause CONTAINER` | Suspendre un conteneur
`docker unpause CONTAINER` | Reprendre un conteneur
`docker rm CONTAINER` | Détruire un conteneur

## **Gestion des conteneurs en vrac**

COMMAND | DESCRIPTION
---|---
`docker stop $(docker ps -q)` | Pour arrêter tous les conteneurs en cours d'exécution
`docker stop $(docker ps -a -q)` | Pour arrêter tous les conteneurs arrêtés et en cours d'exécution
`docker kill $(docker ps -q)` | Pour tuer tous les conteneurs en cours d'exécution
`docker kill $(docker ps -a -q)` | Pour tuer tous les conteneurs arrêtés et en cours d'exécution
`docker restart $(docker ps  -q)` | Pour redémarrer tous les conteneurs en cours d'exécution
`docker restart $(docker ps -a -q)` | Pour redémarrer tous les conteneurs arrêtés et en cours d'exécution
`docker rm $(docker ps  -q)` | Pour détruire tous les conteneurs en cours d'exécution
`docker rm $(docker ps -a -q)` | Pour détruire tous les conteneurs arrêtés et en cours d'exécution
`docker pause $(docker ps  -q)` | Pour suspendre tous les conteneurs en cours d'exécution
`docker pause $(docker ps -a -q)` | Pour suspendre tous les conteneurs arrêtés et en cours d'exécution
`docker start $(docker ps  -q)` | Pour démarrer tous les conteneurs en cours d'exécution
`docker start $(docker ps -a -q)` | Pour démarrer tous les conteneurs arrêtés et en cours d'exécution
`docker rm -vf $(docker ps -a -q)` | Pour supprimer tous les conteneurs, y compris ses volumes, utilisez
`docker rmi -f $(docker images -a -q)` | Pour supprimer toutes les images
`docker system prune` | Pour supprimer toutes les images, conteneurs, caches et volumes en suspens et inutilisés
`docker system prune -a` | Pour supprimer toutes les images utilisées et inutilisées
`docker system prune --volumes` | Pour supprimer tous les volumes Docker

## **Inspecter les conteneurs**

COMMAND | DESCRIPTION
---|---
`docker ps` | Répertorier les conteneurs en cours d'exécution
`docker ps -a` | Répertorier tous les conteneurs, y compris ceux arrêtés
`docker logs CONTAINER` | Afficher une sortie de conteneur
`docker logs -f CONTAINER` | Suivre une sortie de conteneur
`docker top CONTAINER` | Lister les processus exécutés dans un conteneur
`docker diff` | Montrer les différences avec l'image (fichiers modifiés)
`docker inspect` | Afficher les informations d'un conteneur (formaté json)

## **Exécuter des commandes**

COMMAND | DESCRIPTION
---|---
`docker attach CONTAINER` | Attacher à un conteneur
`docker cp CONTAINER:PATH HOSTPATH` | Copier les fichiers du conteneur
`docker cp HOSTPATH CONTAINER:PATH` | Copier les fichiers dans le conteneur
`docker export CONTAINER` | ExExporter le contenu du conteneur (archive tar)
`docker exec CONTAINER` | Exécuter une commande dans un conteneur
`docker exec -it CONTAINER /bin/bash` | Ouvrez un shell interactif dans un conteneur (il n'y a pas de bash dans certaines images, utilisez /bin/sh)
`docker wait CONTAINER` | Attendez que le conteneur se termine et renvoyez le code de sortie

## **Images**

COMMAND | DESCRIPTION
---|---
`docker images` | Répertorier toutes les images locales
`docker history IMAGE` | Afficher l'historique des images
`docker inspect IMAGE` | SAfficher les informations (au format JSON)
`docker tag IMAGE TAG` | Marquer/Tagguer une image
`docker commit CONTAINER IMAGE` | Créer une image (à partir d'un conteneur)
`docker import URL` | Créer une image (à partir d'une archive tar)
`docker rmi IMAGE` | Supprimer des images
`docker pull REPO:[TAG]` | Extraire une image/un dépôt d'un registre
`docker push REPO:[TAG]` | Pousser une image/dépôt vers un registre 
`docker search TEXT` | Rechercher une image sur le registre officiel
`docker login` | Se connecter à un registre
`docker logout` | Se déconnecter d'un registre
`docker save REPO:[TAG]` | Exporter une image/un dépôt sous forme d'archive tar
`docker load` | Charger des images à partir d'une archive tar

## **Volumes**

COMMAND | DESCRIPTION
---|---
`docker volume ls` | Lister tous les volumes
`docker volume create VOLUME` | Créer un volume
`docker volume inspect VOLUME` | Afficher les informations (au format JSON)
`docker volume rm VOLUME` | Détruire un volume
`docker volume ls --filter="dangling=true"` | Répertorier tous les volumes en suspens (non référencés par aucun conteneur)
`docker volume prune` | Supprimer tous les volumes (non référencés par aucun conteneur)


## **Sauvegarder un conteneur**
Sauvegardez les données Docker à partir des volumes du conteneur et regroupez-les dans une archive tarball :

- `docker run --rm --volumes-from CONTAINER -v $(pwd):/backup busybox tar cvfz /backup/backup.tar CONTAINERPATH`

Une sauvegarde automatisée peut également être effectuée par ce [playbook Ansible](https://github.com/thedatabaseme/docker_backup).
La sortie est également un tar (compressé). Le playbook peut également gérer la conservation des sauvegardes.
Les anciennes sauvegardes seront donc automatiquement supprimées.

Pour créer et sauvegarder également la configuration du conteneur lui-même, vous pouvez utiliser « docker-replay » pour cela. Si tu perds
le conteneur entier, vous pouvez le recréer avec l'exportation depuis `docker-replay`.
Un didacticiel plus détaillé sur l'utilisation de docker-replay peut être trouvé [ici](https://thedatabaseme.de/2022/03/18/shorty-generate-docker-run-commands-using-docker-replay/).

## **Restaurer le conteneur à partir d'une sauvegarde**
Restaurez le volume avec une archive tarball :

- `docker run --rm --volumes-from CONTAINER -v $(pwd):/backup busybox sh -c "cd CONTAINERPATH && tar xvf /backup/backup.tar --strip 1"`