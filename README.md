# Administration et sécurisation de serveur web

## Développeurs :
- DA SILVA SOUSA Pedro (PedroDSS)
- KATASI Justin (justinDev91)
- HAMCHERIF Ilyesse (ihbzk)

## Contexte
Héberger un projet web qui comprend à minima un front, un back et une base de données.
Mise en place de toutes les bonnes pratiques vues en cours pour faire cet hébergement et préparer une présentation technique pour expliquer comment nous l'avons fait.

## Notation:
- Qualité de la soutenance (3 points)
- Un vrai nom de domaine (1 point)
- Un hébergement sur une plateforme de votre choix (2 points)
- Un accès HTTPS sur au moins le front et un adminer (2 points)
- Un accès sécurisé au serveur via SSH (2 points)
- Certificat SSL avec Certbot avec renouvellement automatique (2 points)
- Un fail2ban qui surveille les logs du NGINX et les logs de Adminer (2 points)
- Un accès SMB (SAMBA) pour récupérer/déposer des fichiers (3 points)
- Une question de cours par personne (3 points)


## Configuration de Fail2Ban
Pour surveille les logs du NGINX et les logs de Adminer 
Le service `fail2ban` a été ajouté à notre configuration Docker Compose pour surveiller les logs de `nginx`, `adminer`. Les fichiers de configuration de `fail2ban` se trouvent dans le répertoire `fail2ban` à la racine du projet.

### Fichiers de configuration
- `jail.local`: Configure les jails pour `nginx`, `adminer``.
- `filter.d/nginx-http-auth.conf`: Filtre pour les échecs d'authentification HTTP Nginx.
- `filter.d/nginx-botsearch.conf`: Filtre pour détecter les bots sur Nginx
- `filter.d/nginx-limit-req.conf`: Limiter les requêtes échouées sur Nginx
- `filter.d/adminer.conf`: Filtre pour les échecs d'accès Adminer.

Ces fichiers permettent à `fail2ban` de surveiller les tentatives de connexion échouées, de limiter les requêtes échouées et de bannir les adresses IP suspectes.

### cap_add
La section accorde au conteneur des privilèges supplémentaires qui ne sont pas inclus par défaut. Voici ce que fait chaque fonctionnalité :


## Configuration de Nginx et Certbot
Le serveur Nginx est configuré pour servir notre application web avec un certificat SSL généré par Certbot. La configuration de Nginx inclut deux serveurs : un pour rediriger le trafic HTTP vers HTTPS et un pour servir le trafic HTTPS avec le certificat SSL.

### Fichiers de configuration
- `nginx/default.conf` : Configuration de Nginx pour rediriger HTTP vers HTTPS et servir les pages avec SSL.

### Contenu du fichier `nginx/default.conf`
Serveur HTTP :
* Écoute sur le port 80.
* Redirige tout le trafic vers HTTPS.
* Gère les défis ACME de Certbot pour le renouvellement des certificats SSL.

Serveur HTTPS :
* Écoute sur le port 443 avec SSL activé.
* Utilise les certificats SSL générés par Certbot.
* Proxy les requêtes vers les services symfony et adminer.

### Volumes
Les volumes montés permettent à Nginx d'accéder aux fichiers de configuration et aux certificats SSL :
- `./nginx:/etc/nginx/conf.d` : Monte les configurations de Nginx.
- `./src:/var/www/html` : Monte le répertoire du projet.
- `/var/log/nginx:/var/log/nginx` : Monte les logs de Nginx.
- `/var/www/certbot:/var/www/certbot` : Monte le répertoire pour les défis ACME.
- `/etc/letsencrypt:/etc/letsencrypt` : Monte les certificats SSL générés par Certbot.
  
## Configuration de Certbot
Certbot est utilisé pour obtenir et renouveler automatiquement les certificats SSL. La configuration du service Certbot dans le fichier `docker-compose.yml` est définie pour exécuter le renouvellement des certificats toutes les 12 heures.

### Volumes
- `./certbot/conf:/etc/letsencrypt`: Monte le répertoire de configuration de Certbot.
- `./certbot/www:/var/www/certbot`: Monte le répertoire pour les défis webroot.
  
### Entrypoint
- Renouvellement automatique:
Un script shell renouvelle les certificats toutes les 12 heures en utilisant la méthode du défi webroot.

## Configuration de SSH
Pour sécuriser l'accès SSH, nous avons modifié le fichier /etc/ssh/sshd_config.

### Modifications
* Changer le port SSH : Utilisation d'un port non standard pour éviter les attaques automatisées.
* Désactiver l'authentification par mot de passe : Seule l'authentification par clé publique est autorisée.
* Restreindre les utilisateurs pouvant se connecter : Limiter l'accès à des utilisateurs spécifiques.
* Désactiver l'accès root : Interdire les connexions SSH pour l'utilisateur root.
* Ces configurations garantissent un accès sécurisé au serveur, limitant les risques de compromission via SSH.
