# Vagrant

----
## Qu'est ce que Vagrant ?

[Vagrant](https://www.vagrantup.com/) est un logiciel libre et open-source pour la création et la configuration des environnements de développement virtuels.

On peut l'assimiler au fonctionnement de VirtualBox par exemple.

>Pour plus d'informations se référer au site de [Vagrant](https://www.vagrantup.com/) ou bien voir [Wikipedia](https://fr.wikipedia.org/wiki/Vagrant).

----
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

----
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

----
### Installer des paquets

    sudo apt-get install <package>

Installe un paquet sur la machine virtuelle avec les droits d'administrateurs (ne fonctionne que pour un paquet).

----
### Aller vers l'utilisateur Root

    sudo su

Change d'utilisateur vers l'utilisateur Root.

----
### Dossier des projets

    /var/www/html

Là où sont stockés tous les projets web.

En tapant l'adresse du serveur (de base `192.168.33.10`), voir si un affichage de base s'affiche.

----
### Variables d'environnement

    sudo nano /etc/apache2/envvars

Ici sont placés toutes les variables d'environnement.

Chercher la ligne contenant `APACHE_RUN_USER` et `APACHE_RUN_GROUP` pour changer leurs valeur de base `www-data` par `ubuntu` qui correspond à des noms.

----
### Relancer des services

    sudo <serviceName> restart

ou bien

    sudo <serviceName> reload

Utiliser la commande pour que le service soit relancé ce qui permet de prendre en compte les modifications faites.

----
### Changer de propriétaire

    sudo chown -R USER:GROUP ../html/

Cette commande permet de changer le projet de propriétaire, la commande `-R` permet de faire cette commande de façon récursive (interagit avec tous les fichiers et dossiers du dossier d'origine, ici `../html/`).

Elle est utile si une copie d'un repository Git n'est pas autorisée.

----
### Tout supprimer

    rm -rf *

----
### Arrêt de la machine vagrant

    vagrant halt

----
## Erreurs

Si une erreur survient lors du premier lancement (commande `up`) de la machine, télécharger la [version 5.1.30 de VirtualBox](http://download.virtualbox.org/virtualbox/5.1.30/VirtualBox-5.1.30-118389-OSX.dmg) (erreur apparue sous Mac).

Si un projet Git ne peut pas être copié, se référé à la section `Variables d'environnement`.

----
## Commandes pratiques en ligne de commandes

    ll

Raccourci de `ls-all`.

    ctrl + R

Permet de retrouver une ancienne commande déjà taper sans utiliser les flèches du haut.

    command + T

Ouvrir plusieurs consoles.

    git clone <project> ./

Permet de cloner juste le contenu du repository.
