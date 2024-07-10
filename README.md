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