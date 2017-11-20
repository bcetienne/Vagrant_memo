# Vagrant et PHP
> Auteur : Etienne Blanc-Coquand

> Cours : Morgan Corroyer

----
----

## Qu'est ce que Vagrant ?

[Vagrant](https://www.vagrantup.com/) est un logiciel libre et open-source pour la création et la configuration des environnements de développement virtuels.

On peut l'assimiler au fonctionnement de VirtualBox par exemple.

> Pour plus d'informations, se référer au site de [Vagrant](https://www.vagrantup.com/) ou bien voir [Wikipedia](https://fr.wikipedia.org/wiki/Vagrant).

## Qu'est ce que Apache ?

Le logiciel libre Apache HTTP Server est un serveur HTTP. C'est le serveur HTTP le plus célèbre sur Internet.

> Pour plus d'informations, se référer au site de [Apache](https://httpd.apache.org/) ou bien voir [Wikipedia](https://fr.wikipedia.org/wiki/Apache_HTTP_Server).

## Qu'est ce que MySQL ?

MySQL est un système de gestion de base de données relationnelles (SGBDR). Il fait parti des logiciels de gestion de base de données les plus utilisés au monde autant par le grand public que les professionnels.

> Pour plus d'informations, se référer au site de [MySQL](https://www.mysql.com/) ou bien voir [Wikipedia](https://fr.wikipedia.org/wiki/MySQL).

## Qu'est ce que PHP ?

[PHP](http://php.net/) est un langage de programmation côté serveur, Orienté Objet, principalement utilisé pour produire des pages web dynamiques via un serveur HTTP.

> Pour plus d'informations, se référer à la [documentation officielle de PHP](http://php.net/docs.php) ou bien aller voir [Wikipedia](https://fr.wikipedia.org/wiki/PHP).

### Ces trois technologies réunis sous une seule même machine forment [la machine VAMP que l'on peut voir plus bas](#vampMachine).

----
----

## Installation et mise en place d'une machine Vagrant

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

|Commandes   	                  |Explication   	                                                                      |
|---------------------------------|---------------------------------------------------------------------------------------|
|`vagrant up`  	                  |Permet de lancer la machine virtuelle ou bien de télécharger la machine qui a été donnée dans le fichier de configuration.                                                                                         |
|`vagrant destroy`	              |Détruire une machine.                                                                  |
|`vagrant global-status`          |Voir l'état actuelle de la machine virtuelle.                                          |
|`vagrant ssh`                    |Se connecter à la machine virtuelle via SSH.                                           |
|`exit`                           |Quitter le mode SSH ou l'utilisateur `root`.	                                          |
|`vagrant reload`                 |Relancer la machine virtuelle.	                                                      |
|`sudo apt-get install <package>` |Installe un paquet sur la machine virtuelle avec les droits d'administrateurs (ne fonctionne que pour un paquet).	                                                                                       |
|`sudo su`                        |Change d'utilisateur vers l'utilisateur Root.                                          |
|`rm -rf *`                       |Tout supprimer.	                                                                      |
|`vagrant halt`                   |Eteindre une machine.	                                                              |
|`sudo service <serviceName> restart` ou bien `sudo service <serviceName> reload`|Utiliser un des deux commandes pour que le service en question soit relancé ce qui permet de prendre en compte les modifications faites.                             |
|`sudo chown -R USER:GROUP ../html/`|Cette commande permet de changer le projet de propriétaire, la commande `-R` permet de faire cette commande de façon récursive (interagit avec tous les fichiers et dossiers du dossier d'origine, ici `../html/`). Elle est utile si une copie d'un repository Git n'est pas autorisée                           |

### Dossier des projets

    /var/www/html

Là où sont stockés tous les projets web.

En tapant l'adresse du serveur (de base `192.168.33.10`), voir si un affichage de base s'affiche.


### Variables d'environnement

    sudo nano /etc/apache2/envvars

Ici sont placés toutes les variables d'environnement.

Chercher la ligne contenant `APACHE_RUN_USER` et `APACHE_RUN_GROUP` pour changer leurs valeur de base `www-data` par `ubuntu` qui correspond à des noms.


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


## Faire une image de sa machine et la réutiliser
### Créer une image

    vagrant package

Permet d'exporter sa machine virtuelle qui peut être réimportable par la suite.

Suite à cette commande, celle-ci produit un fichier qui se nomme `package.box`, celle-ci est une image compressée de la machine, elle contient la configuration de la machine (`Vagrantfile`), les applications (`Apache`, `PHP`,...), les fichiers de configurations (`.conf`) mais ne sont pas conservés les projets qui se synchronisent.

Pour recréer exactement la même machine, copier sur une clé USB par exemple, les fichiers `Vagrantfile`, le dossier `data` et l'image de la machine `package.box`.

### Réutiliser une image

Se déplacer dans le dossier créé qui contient les 3 éléments nécessaires pour refaire une copie parfaite de la machine en image.

Utiliser la commande `vagrant box add <nomImage> <chemin>` pour installer l'image sur le système.

- `nomImage`: Informations qui permettra de la retrouver plus rapidement (n'importe quel nom est accepté comme phpBox, baseBox, ...).

- `chemin`: Chemin vers le fichier package.box qui contient la machine (exemple : `./package.box`).

Ouvrir le fichier `Vagrantfile`, renommer la ligne commençant par `config.vm.box` par le nom donné sur la commande ci-dessus (exemple : Si dans la commande ci-dessus le `nomImage` est égal à `baseBox` alors, remplacer la valeur entre `"` par `baseBox`. Puis terminer par la commande `vagrant up` pour lancer l'installation de la machine.

## Configuration PHP
### Afficher les erreurs

Dans la Vagrant, aller dans le fichier `sudo nano /etc/php/7.0/apache2/php.ini`.

Rechercher la ligne `display_errors = Off` et remplacer `Off` par `On`.

Rechercher la ligne `display_startup_errors = Off` et remplacer `Off` par `On`.

Redémarrer le service Apache

## Applications

> Juste la fin de la commande est écrit, ajouter le `sudo apt-get install <Commande>`

|Commande   	        |Application  	                                                                        |
|-----------------------|---------------------------------------------------------------------------------------|
|`apache2`  	        |Apache 2 - Serveur HTTP	                                                            |
|`php7.0`  	            |PHP7 -	Langage de script côté serveur                                                  |
|`libapache2-mod-php7.0`|Mod pour PHP7	                                                                        |
|`mysql-server`         |Serveur MySQL	                                                                        |
|`php7.0-mysql`         |Active les modules MySQL pour PHP                                                            


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
|`ctrl + W`             |Chercher une chaîne de caractères.                                                     |
|`command + T`          |Ouvrir plusieurs consoles.	                                                            |
|`git clone <projet> ./`|Permet de cloner juste le contenu du repository.	                                    |
|`sudo !!`              |Permet de réutiliser une commande qui n'a pas fonctionné sans les droits d'admins en utilisant cette fois-ci les droits d'administration.	                                                                          |

## <a id="vampMachine"></a>Créer une Vagrant orienté web (VAMP).

Créer le dossier VAMP, se déplacer dans le dossier VAMP, créer un dossier data et faire la commande `vagrant init ubuntu/xenial64` pour automatiquement ajouter l'image de la machine.

Ouvrir le fichier `Vagrantfile`, modifier la ligne 35 en enlevant le commentaire et en mettant l'adresse de la machine, ici `config.vm.network "private_network", ip: "192.168.33.10"` et la ligne 46 en enlevant le commentaire et en mettant le dossier `data` qui sera synchronisé avec le chemin spécifié du dossier sur la machine Vagrant, ici `config.vm.synced_folder "./data", "/var/www/html/"`.

Démarrer la machine toujours depuis le dossier VAMP avec la commande `vagrant up` pour installer l'image.

Se connecter à la machine via SSH avec la commande `vagrant ssh` et installer les paquets de PHP, PHPMyAdmin, Apache2 et MySQL avec la commande suivante `sudo apt-get install apache2 php7.0 libapache2-mod-php7.0 mysql-server php7.0-mysql`.

Entrer le mot de passe de la base MySQL, sélectionner Apache2.

Se déplacer dans le dossier `var/www/html/` et vérifier avec la commande `ls` que le fichier `index.html` existe, modifier les utilisateurs d'Apache dans le fichier `/etc/apache2/envvars` et modifier par `export APACHE_RUN_USER=ubuntu export APACHE_RUN_GROUP=ubuntu` faire ensuite la commande `sudo chown -R ubuntu:ubuntu ../html/`, relancer Apache avec la commande `sudo service apache2 restart` supprimer le fichier `index.html` avec la commande `rm -rf index.html`.

Activer les affichages d'erreurs de PHP en modifiant le fichier `/etc/php/7.0/apache2/php.ini`, remplacer les valeurs `Off` par `On` sur les lignes suivantes `display_errors` et `display_startup_errors`, relancer Apache.

Pour modifier l'heure et avoir l'heure locale, faire la commande `sudo dpkg-reconfigure tzdata` et choisir sa région.

Pour avoir les informations de connexion à la base de données, taper la commande `vagrant ssh-config`.

----

## Liste d'outils utiles

- [Vagrant](https://www.vagrantup.com/) et bien évidemment [VirtualBox](https://www.virtualbox.org/) qui permettent la virtualisation des serveurs en règle générale.

- [Outils JetBrains](https://www.jetbrains.com/) comme [PhpStorm](https://www.jetbrains.com/phpstorm/) ou bien [DataGrio](https://www.jetbrains.com/datagrip/) qui sont tous deux des IDE très puissants et performants qui permettent, respectivement, de gérer des projets PHP en pouvant s'occuper en polyvalence de ce qui est CSS, HTML, Javascript, SQL, ... et l'autre IDE qui est beaucoup plus axé sur la gestion des base de données.

- [MySQL Workbench](https://www.mysql.com/fr/products/workbench/) ou bien [Toad for MySQL](https://www.toadworld.com/products/toad-for-mysql) qui sont tous les deux de très bons outils permettant de gérer des bases de données.

- [Atom](https://atom.io/) ou bien [Sublime Text](https://www.sublimetext.com/) qui sont tous les deux des éditeurs de texte qui peuvent gérer les différents langages du web et qui peuvent avoir différents paquets installés pour avoir de plus amples fonctionnalitées.

