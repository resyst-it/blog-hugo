+++
date = "2016-03-02"
title = "Netcat"
subtitle = "Présentation de netcat pour le scan de port"
noread = true
socialsharing = true
totop = true
author = "JCF"
tags = [ "Linux", "Netcat" ]
categories = [ "Linux" ]
#thumbnail = "img/dock.jpeg"
#authortwitter = "https://twitter.com/El_pr0fes0r"
#bannerinline = "../../img/docki.jpeg"
#banner = "../../img/dock.png"

+++

D'après la définition de wikipedia "netcat est un utilitaire permettant d'ouvrir des connexions réseau". Il est aussi généralement appélé le couteau suisse réseau.
Je me sert de netcat assez réguliérement dans mes missions de tous les jours. Occupant un poste d'admin sys/réseau et infogérant des plateformes clientes, j'ai réguliérement ce genre de demande :

- Vous êtes sur que le port est ouvert sur le firewall je n'arrive pas à me connecter
- J'ai fait un telnet et ça ne marche pas (Hey oui si tu ne sais pas utiliser telnet correctement ça ne marche pas)

Comme vous l'avez compris je fais essentiellement du scan de port avec netcat. Donc je vais vous décrire différentes façon d'utiliser netcat pour du scan de port. Je trouve netcat plus simple que nmap, la syntaxe de la commande se retient plus facilement. Après c'est une question de goût et d'habitude.
On verra aussi comment transférer des fichiers avec netcat.

## 1. Utilisation simple de netcat

Dans son approche basic, netcat permet d'ouvir une connexion réseau entre la machine et le serveur.  
Par exemple :

    nc blog.resyst-it.fr 80

Cette commande permet d'ouvrir une connexion TCP sur le port 80. Maintenant, cette connexion réseau est ouverte. Vous pouvez éxecuter des commandes pour "discuter" avec le serveur par exemple. Mais ce sujet n'est pas dans notre scope.

## 2. Netcat pour le scan de port

Voici un exemple pour le scan d'un port :

    nc -zv blog.resyst-it.fr 80
    Connection to blog.resyst-it.fr 80 port [tcp/http] succeeded!

On peut voir que le port 80 est ouvert.

    nc -zv blog.resyst-it.fr 80
    nc: connect to blog.resyst-it.fr port 80 (tcp) failed: Connection refused

Et dans ce cas là, le port est fermé.

L'option :  
-v : Permet d'activer le mode verbose  
-z : Spécifie à netcat qu'il doit juste faire un scan. Il ne gardera pas de session ouverte comme dans l'utilisation simple de netcat

Nous pouvons aussi désativer la résolution DNS :

    nc -nvz blog.resyst-it.fr

L'option :  
-n : Permet de ne pas faire de résolution DNS

Donc si on le fait sur blog.resyst-it.fr ça ne fonctionnera pas.

    nc -nzv blog.resyst-it.fr 80
    nc: getaddrinfo: Name or service not known

Alors que sur l'IP, pas de problème :

    nc -nvz 37.187.126.209 80   
    Connection to 37.187.126.209 80 port [tcp/*] succeeded!

  
## 3. Mode client/serveur

Nous pouvons utiliser netcat en mode client/serveur et non pas simplement en mode client. Nous pouvons dire à netcat d'écouter sur un port pour vérifier que le firewall entre nos 2 serveurs ne bloque pas la communication.

Sur le serveur :

    nc -vl 192.168.122.87 -p 1234
    listening on [any] 1234 ..

Sur le client :

    nc -nvz 192.168.122.87 1234

Le résultat sur le client :

    (UNKNOWN) [192.168.122.87] 1234 (?) open

UNKNOWN car la machine ne peut pas mettre un nom sur le service ecoutant sur le port 1234  
Vu que le client fait juste un scan (option -z) la connexion est directement fermé.

## 4. Netcat pour le transfére de fichiers

Netcat permet aussi de faire du transfère de fichier.  

Pour un simple fichier texte sur la machine devant recevoir le fichier :

    nc -l -p 1234 > test

Sur la machine qui envoie le fichier :

    nc -q 0 192.168.122.87 1234 < myfile

Mais ces commandes fonctionnent aussi avec une image ISO.

Sur la machine recevant l'image ISO :

    nc -l -p 1234 > ubuntu.iso

Sur la machine envoyant l'image ISO :

    nc -q 0 192.168.122.87 1234 < ubuntu-15.10-desktop-amd64.iso

Si on vérifie l'empreinte de l'image.

Sur la machine envoyant l'image ISO :

    md5sum ubuntu-15.10-desktop-amd64.iso 
    ece816e12f97018fa3d4974b5fd27337  ubuntu-15.10-desktop-amd64.iso

Sur la machine recevant l'image ISO :

    md5sum ubuntu.iso
    ece816e12f97018fa3d4974b5fd27337  ubuntu.iso

On voit que tout s'est transféré correctement.

L'option :  
-q 0 (zéro) permet de couper la connxion dès que netcat détecte la fin du fichier a transférer.

## 5. Conclusion

Je pense que vous avez la une bonne approche de netcat dans une utilisation régulière. Ce scope couvre presque 90% de l'utilisation netcat en entreprise pour un sysadmin.
Je vous laisse faire des tests et nous faire un retour concernant son utilisation ;)

Pour rappel :  
nc -nvz IP PORT -> pour le scan d'un port sur une IP  
nc -vz NOM PORT -> pour le scan d'un port sur une URL

