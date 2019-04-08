# AUDITER DES ARCHITECTURES DOCKER

- [AUDITER DES ARCHITECTURES DOCKER](#auditer-des-architectures-docker)
  - [VERIFICATION DES ARCHITECTURES DOCKER](#verification-des-architectures-docker)
      - [Vérification du non-partage des namespaces](#v%C3%A9rification-du-non-partage-des-namespaces)
      - [Gestion des mises à jour](#gestion-des-mises-%C3%A0-jour)
      - [Vérification des paramètres du pare-feu](#v%C3%A9rification-des-param%C3%A8tres-du-pare-feu)
      - [Vérification des commandes autorisés à docker](#v%C3%A9rification-des-commandes-autoris%C3%A9s-%C3%A0-docker)
      - [Partition séparée pour Docker](#partition-s%C3%A9par%C3%A9e-pour-docker)
      - [Limite des PID](#limite-des-pid)
      - [Vérification des permissions](#v%C3%A9rification-des-permissions)
    - [Environnement Docker](#environnement-docker)
    - [Conteneurs](#conteneurs)
      - [Politique de sécurité](#politique-de-s%C3%A9curit%C3%A9)
      - [Réseau](#r%C3%A9seau)
      - [Récupération des images](#r%C3%A9cup%C3%A9ration-des-images)
      - [Politique de redémarrage](#politique-de-red%C3%A9marrage)
      - [Réseaux](#r%C3%A9seaux)
    - [Performances](#performances)
      - [Limite de la mémoire disponible pour les conteneurs](#limite-de-la-m%C3%A9moire-disponible-pour-les-conteneurs)
      - [Limite d’utilisation du SWAP](#limite-dutilisation-du-swap)
      - [Limite d’usage de la mémoire du Kernel](#limite-dusage-de-la-m%C3%A9moire-du-kernel)
      - [Verification « out of memory » des règles du Kernel](#verification-%C2%AB-out-of-memory-%C2%BB-des-r%C3%A8gles-du-kernel)
      - [Limite des appels au kernel.](#limite-des-appels-au-kernel)
      - [Mettre des limites aux ressources system (anti DOS)](#mettre-des-limites-aux-ressources-system-anti-dos)
      - [Restreindre les accès aux réseaux.](#restreindre-les-acc%C3%A8s-aux-r%C3%A9seaux)
    - [Monitoring](#monitoring)
      - [Gestion du monitoring](#gestion-du-monitoring)
      - [Récupération des logs](#r%C3%A9cup%C3%A9ration-des-logs)
      - [Exportation des logs](#exportation-des-logs)
      - [Analyse des logs](#analyse-des-logs)
  - [Scripts](#scripts)
    - [Docker Bench for Security](#docker-bench-for-security)
    - [Docker Slim](#docker-slim)

## VERIFICATION DES ARCHITECTURES DOCKER

###	Environnement linux

#### Vérification du non-partage des namespaces

- S'assurer que l'espace nom de l'hôte n'est pas partagé.
- S'assurer que l'espace de noms de processus de l'hôte n'est pas partagé.
- S'assurer que l'espace de noms IPC de l'hôte n'est pas partagé.
- S'assurer que l'espace de noms UTS de l'hôte n'est pas partagé.
- S'assurer que les espaces de noms d'utilisateur de l'hôte ne sont pas partagés.

#### Gestion des mises à jour

- Les conteneurs sont à jour
- L’hôte est à jour
- La version de Docker est à jour
  
#### Vérification des paramètres du pare-feu

- Seuls les conteneurs autorisés sont accessibles depuis l’extérieur.
- Aucune base de données n’est accessible depuis l’extérieur

#### Vérification des commandes autorisés à docker

- S’assurer que Docker n’a pas les droits d’exécuter des commandes dangereuses pour le système.

#### Partition séparée pour Docker

- S'assurer qu'une partition séparée pour les conteneurs a été créée.

#### Limite des PID

- Vérifier qu’il y a bien des limites de PID sur le système hôte.

#### Vérification des permissions

- S'assurer que seuls les utilisateurs de confiance sont autorisés à contrôler le démon Docker.
- S'assurer que les permissions setuid et setgid sont supprimées dans les images.
- Vérifier que les filesystems sont en permission : read-only

-- Docker-service

- Assurez-vous que le propriétaire du fichier docker.service est défini sur root:root.

- Assurez-vous que les permissions du fichier docker.service sont fixées à 644 ou plus restrictives.

-- Docker-socket

- Assurez-vous que le propriétaire du fichier docker.socket est défini sur root:root.
- Assurez-vous que les permissions du fichier docker. socket sont fixées à 644 ou plus restrictives.

-- Fichier de certificat d'enregistrement

- Assurez-vous que la propriété du fichier de certificat de registre est définie à root:root 
- S'assurer que les permissions des fichiers de certificat de registre sont fixées à 444 ou plus restrictives. 

-- Dossier Docker /etc/docker

- Assurez-vous que les permissions du répertoire /etc/docker sont fixées à 755 ou plus restrictives.
- Assurez-vous que la propriété du répertoire /etc/docker est définie à root:root

### Environnement Docker

- Vérifier que la variable d’environnement docker : DOCKER_CONTENT_TRUST est activée (pour ne récupérer que les images signées).
- Vérifier que les conteneurs ne sont pas lancés en ROOT.
- Vérifier que aucun répertoire sensible du système hôte ne sont montés sur des conteneurs.

### Conteneurs

#### Politique de sécurité

- Assurez-vous que les options de sécurité SELinux sont définies.
- S'assurer que le profil AppArmor est activé.
- S'assurer que les conteneur utilisés ne sont pas en mode privilégiés.
- S'assurer que le conteneur ne peut acquérir des privilèges supplémentaires.

#### Réseau

- S'assurer que seuls les orifices nécessaires sont ouverts sur le conteneur.
- S'assurer que le trafic conteneur entrant est lié à une interface hôte spécifique.
- S'assurer que les ports privilégiés ne sont pas cartographiés à l'intérieur des conteneurs.
 

#### Récupération des images

- Vérifier la provenance des images.
- Vérifier le checksum de l’image après la récupération.
- Spécifier le hash et la version lors de la récupération d’une image. 
- Analyser le Dockerfile de l’image.
- Vérifier que les images utilisées sont à jour. 

#### Politique de redémarrage

- S'assurer que la politique de redémarrage des conteneurs en cas de défaillance est fixée à " 5 ".

#### Réseaux

- S’assurer que le(s) réseau(x) utilisé(s) par les conteneurs est (sont) chiffré(s) (docker network -d overlay -o encrypted mySecureNetwork) 

### Performances

#### Limite de la mémoire disponible pour les conteneurs

#### Limite d’utilisation du SWAP

#### Limite d’usage de la mémoire du Kernel

#### Verification « out of memory » des règles du Kernel

#### Limite des appels au kernel.

- S'assurer que les capacités du noyau Linux sont limitées dans les conteneurs.

#### Mettre des limites aux ressources system (anti DOS)

#### Restreindre les accès aux réseaux.

### Monitoring

#### Gestion du monitoring

- Assurez-vous que l'audit est configuré pour le démon Docker.
- S'assurer que l'audit est configuré pour les fichiers et répertoires Docker
- Assurez-vous que l'audit est configuré pour les fichiers et répertoires Docker
- Assurez-vous que l'audit est configuré pour les fichiers et répertoires Docker - docker.service
- Assurez-vous que l'audit est configuré pour les fichiers et répertoires Docker - docker.socket.
- S'assurer que l'audit est configuré pour les fichiers et répertoires de Docker - /etc/default/docker
- S'assurer que l'audit est configuré pour les fichiers et répertoires Docker - /etc/docker/daemon.json.json
- S'assurer que l'audit est configuré pour les fichiers et répertoires Docker - /usr/bin/docker-**. 
- Vérifier que les logs de l’hôte sont vérifiés.

#### Récupération des logs

- Assurez-vous que le niveau d'enregistrement est réglé sur 'info'.
- S'assurer que l'état de santé du conteneur est vérifié au moment de l'exécution.

#### Exportation des logs

- S’assurer que les logs sont exportés à l’extérieur du conteneur.
- Vérifier que les logs ne seront pas perdus en cas de problèmes avec le serveur. 

#### Analyse des logs

- Vérifier la possibilité de pouvoir analyser les logs.
- S’assurer que les logs sont traités.
- S’assurer qu’en cas d’erreur, un système est mis en place pour protéger le système en place.

## Scripts

### Docker Bench for Security

Docker Bench Security est un script disponible sur le dépôt dépôt github de Docker permettant de réaliser un premier audit de sécurité sur l’ensemble des conteneurs Docker du serveur. Ce script va ainsi vérifier une bonne partie des tests citées dans la partie précédente. 

Pour en savoir plus :  [github](https://github.com/docker/docker-bench-security).

### Docker Slim

Docker slim permet de réduire la taille des images Docker pour les optimiser. De plus, Docker Slim permet de créer de façon automatique les profils de sécurité AppArmor et secomp (secure computing mode).


Pour en savoir plus : [github](https://github.com/docker-slim/docker-slim)
Et :  [dockersl.im](http://dockersl.im/)
