# Premiers Pas avec Traefik

## 1. Lancement de Traefik
1. Par votre terminal deplacer vous dans le dossier `01-Traefik-Aperçu`
2. Ouvrez le fichier`docker-compose.yml` avec votre editeur de texte, pour constaer comment docker lancera Traefik
3. Dans notre dossier `01-Traefik-Overview` executer cette commande -> `docker-compose up -d`
4. Surveillez vos logs avec docker `docker-compose logs`(Cette commande log print toute la stack)

##  2. Ajouter une service à Traefik
1. Decommnenter la section `WEBSERVERS` dans le fichier `docker-compose.yml`. Pour obtenir ceci.

```yaml
#########################################################
## WEBSERVERS
#########################################################
  whoami:
    image: traefik/whoami
    labels: 
      - "traefik.enable=true"
      - "traefik.http.routers.whoami.rule=Host(`whoami.localhost`)"
```

2. Enfin `docker-compose up -d`
3. Ouvrez une page de votre navigateur avec l'adresse : `whoami.docker.localhost`  ou depuis un terminal `curl -H Host:whoami.docker.localhost http://127.0.0.1` ensuite vous obtiendre ceci mais avec vos adresses.

```yml
Hostname: 931789a57923
IP: 127.0.0.1
IP: 172.20.0.3
RemoteAddr: 172.20.0.2:46648
GET / HTTP/1.1
Host: whoami.docker.localhost
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_6) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/84.0.4147.89 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Accept-Encoding: gzip, deflate, br
Accept-Language: en-GB,en-US;q=0.9,en;q=0.8
Cache-Control: max-age=0
Sec-Fetch-Dest: document
Sec-Fetch-Mode: navigate
Sec-Fetch-Site: none
Sec-Fetch-User: ?1
Upgrade-Insecure-Requests: 1
X-Forwarded-For: 172.20.0.1
X-Forwarded-Host: whoami.docker.localhost
X-Forwarded-Port: 80
X-Forwarded-Proto: http
X-Forwarded-Server: 07d5bffe1678
X-Real-Ip: 172.20.0.1

```

##  3. Scale le service Whoami
1. Par votre terminal deplacer vous dans le dossier `01-Traefik-Aperçu`
2. Nous allons scale le service `whoami` afin d'en obtenir 3 instances en tapant `docker-compose scale whoami=3`
3. Ouvrez une page de votre navigateur avec l'adresse : `whoami.docker.localhost`  ou depuis votre terminal : `curl -H Host:whoami.docker.localhost http://127.0.0.1` avec un refresh l'adresse devrait changer entant donnée que l'on à 3 instances pour le même service.

##  4. Rencontre avec le Dashboard
1. Ouvrez une fenetre : http://0.0.0.0:8080 pour acceder au panel Traefik

## 5. Decouverte des réseaux et des volumes avec Traefik
````yml
---
version: '3'

########################### NETWORK
networks:
  traefik:

########################### VOLUMES
volumes:


services:
````

# Continuer pour la Configration de Traefik

### Clique ici -> [02-Traefik-Configuration](https://github.com/M0okz/Traefik-Udemy/tree/main/02-Traefik-Configuration/traefik-configuration.md)