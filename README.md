# Vagrant
> Auteur : Etienne Blanc-Coquand

> Cours : Morgan Corroyer

## Qu'est ce que Vagrant ?

[Vagrant](https://www.vagrantup.com/) est un logiciel libre et open-source pour la création et la configuration des environnements de développement virtuels.

On peut l'assimiler au fonctionnement de VirtualBox par exemple.

>Pour plus d'informations se référer au site de [Vagrant](https://www.vagrantup.com/) ou bien voir [Wikipedia](https://fr.wikipedia.org/wiki/Vagrant).


## Installation et mise en place

Aller sur le site de Vagrant pour [télécharger la version](https://www.vagrantup.com/downloads.html) compatible avec l'OS.

Pour initialiser une machine Vagrant, créer un dossier vide, se placer dedans et lancer la commande :

    vagrant init

Un fichier de base a été créé sous le nom `Vagrantfile`, l'ouvrir et chercher la ligne commençant par `config.vm.box` et changer sa valeur par défaut par la machine que l'on veut avoir sur Vagrant (exemple : "ubuntu/xenial64", cette machine peut être retrouvée sur [ce site](https://app.vagrantup.com/boxes/search) comme d'autres machines).

Toujours dans le même fichier, supprimer le `#` pour décommenter la ligne `config.vm.network "private_network", ip: "private_ip"` (`private_ip` est a changer par l'adresse privée que l'on veut).

Sauvegarder puis quitter le fichier de configuration.

### Liaison entre machine virtuelle et local

L'utilité de créer une liaison entre le projet sur le serveur de développement en machine virtuelle et le projet sur la machine locale, permet de synchroniser les modifications faites au projet bidirectionnellement.

Pour cela, dans le dossier local créer un dossier qui se nomme par convention `data`, dedans, copier son projet ou bien l'importer via Git.

Par la suite, modifier le fichier `Vagrantfile` et chercher la ligne qui contient `config.vm.synced_folder`, enlever le `#` pour la décommenter et remplacer ses valeurs par défaut par `./data`(qui représente le dossier du projet en local) et `/var/www/html`(qui représente le dossier du projet en distant sur le serveur de développement en machine virtuelle).

Sauvegarder, quitter le fichier de configuration et relancer Vagrant avec la commande `vagrant reload`.


## Utilisation et configuration du serveur

>N.B. : Préfixer toutes les commandes par `vagrant`.

    vagrant up

Permet de lancer la machine virtuelle ou bien de télécharger la machine qui a été donnée dans le fichier de configuration.


    vagrant destroy

Détruire une machine.


    vagrant global-status

Voir l'état actuelle de la machine virtuelle.


    vagrant ssh

Se connecter à la machine virtuelle via SSH.


    exit

Quitter le mode SSH ou l'utilisateur `root`.


    vagrant reload

Relancer la machine virtuelle.


### Installer des paquets

    sudo apt-get install <package>

Installe un paquet sur la machine virtuelle avec les droits d'administrateurs (ne fonctionne que pour un paquet).


### Aller vers l'utilisateur Root

    sudo su

Change d'utilisateur vers l'utilisateur Root.


### Dossier des projets

    /var/www/html

Là où sont stockés tous les projets web.

En tapant l'adresse du serveur (de base `192.168.33.10`), voir si un affichage de base s'affiche.


### Variables d'environnement

    sudo nano /etc/apache2/envvars

Ici sont placés toutes les variables d'environnement.

Chercher la ligne contenant `APACHE_RUN_USER` et `APACHE_RUN_GROUP` pour changer leurs valeur de base `www-data` par `ubuntu` qui correspond à des noms.


### Relancer des services

    sudo service <serviceName> restart

ou bien

    sudo service <serviceName> reload

Utiliser la commande pour que le service soit relancé ce qui permet de prendre en compte les modifications faites.


### Changer de propriétaire

    sudo chown -R USER:GROUP ../html/

Cette commande permet de changer le projet de propriétaire, la commande `-R` permet de faire cette commande de façon récursive (interagit avec tous les fichiers et dossiers du dossier d'origine, ici `../html/`).

Elle est utile si une copie d'un repository Git n'est pas autorisée.


### Tout supprimer

    rm -rf *


### Arrêt de la machine vagrant

    vagrant halt


## Configuration d'Apache2

Pour commencer, ouvrir deux invites de commande, une qui reste sur le poste et une qui est sur le Vagrant via le SSH.

Dans la fenêtre SSH, se déplacer vers le dossier `/etc/apache2/sites-available/` pour voir les fichiers de configurations.

Modifier ou copier (si on compte le réutiliser) ou renommer le fichier `000-default.conf` (ne pas oublier de l'ouvrir avec les droits administrateurs pour que les modifications prennent effet).
> Exemple pour plusieurs projets copier le fichier `000-default.conf` et le coller en le renommant `projet1.conf`, `projet2.conf`,...

Modifier, copier le `documentRoot` qui doit pointer vers le dossier du projet (exemple `/var/www/html/dossierProjet1`) et le serverName qui pointera vers la bonne URL du projet (exemple `projet1.dev`).

Réitérer cette procédure pour chaque projet.

### Créer un domaine fictif personnalisé

Dans la console qui pointe sur le poste (pas sur la Vagrant via SSH, modifier le fichier suivant avec ce chemin `sudo nano /etc/hosts`

Ajouter l'adresse ip de la Vagrant avec un espace et le nom de domaine du projet (Attention à ne pas prendre un nom de domaine déjà existant ex: `192.168.33.10 projet1.dev`), sauvegarder et fermer le fichier, le tester sur le navigateur (ne pas oublier le `http://`).

> N.B. : Pour mettre plusieurs projets sur une même IP, séparer les noms de projet par un espace exemple `192.168.33.10 projet1.dev projet2.dev`.

Suite à tout cela, taper les commandes suivantes :

    sudo a2dissite

Qui permet de désactiver des sites (désactiver un hôte virtuel / les fichiers conf en gros)

> Exemple concret : Si deux fichiers de configuration ont été fait par projet `projet1.conf` et `projet2.conf`, désactiver alors le fichier `000-default.conf`.



    sudo a2ensite

Qui permet d'activer des sites (activer un hôte virtuel / les fichiers conf en gros)

> Exemple concret : Les deux fichiers de configuration qui ont été fait par projet comme ci-dessus, les activer avec cette commande en les espaçant.



Relancer le Apache2 avec la commande `sudo service apache2 reload`.


## Erreurs

- Si une erreur survient lors du premier lancement (commande `up`) de la machine, télécharger la [version 5.1.30 de VirtualBox](http://download.virtualbox.org/virtualbox/5.1.30/VirtualBox-5.1.30-118389-OSX.dmg) (erreur apparue sous Mac).

- Si un projet Git ne peut pas être copié, se référé à la section `Variables d'environnement`.

- Eviter à ce que le poste se mette en veille alors que la Vagrant fonctionne sinon ceci pourrait faire planter la connexion entre la Vagrant et VirtualBox.


## Commandes pratiques en ligne de commandes

|Commande   	        |Explication   	                                                                        |
|-----------------------|---------------------------------------------------------------------------------------|
|`ll`  	                |raccourci de `ls-all`	                                                                |
|`ctrl + R`	            |Permet de retrouver une ancienne commande déjà taper sans utiliser les flèches du haut.|
|`ctrl + K`             |Supprime une ligne dans l'invite de commande.                                          |
|`command + T`          |Ouvrir plusieurs consoles.	                                                            |
|`git clone <projet> ./`|Permet de cloner juste le contenu du repository.	                                    |
|`sudo !!`              |Permet de réutiliser une commande qui n'a pas fonctionné sans les droits d'admins en utilisant cette                          fois-ci les droits d'administration.	                                                  |

## Ordre pour faire une machine Vagrant selon Morgan

Après avoir créé le dossier, installer la machine avec la commande `vagrant up`, après que la machine soit installée, télécharger le paquet de pour `apache2` et modifier les valeurs des utilisateurs d'Apache.

Modifier les utilisateurs avec la commande `chown`, virer le `index.html` présent de base avec Apache.

Retourner dans le dossier de la Vagrant, créer un dossier `data`, cloner le dépôt Git dans le dossier `data`.

Modifier le fichier `Vagrantfile` pour le `sync_folder`, sauvegarder, et relancer la machine avec `vagrant reload`.

Retourner avec la commande `vagrant ssh` dans le serveur, chercher le dossier `/var/www/html` pour voir si tout le dépôt à bien été copié depuis Git.
