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
3. Ouvre une page de votre navigateur avec l'adresse : `whoami.docker.localhost`  ou depuis un terminal `curl -H Host:whoami.docker.localhost http://127.0.0.1` ensuite vous obtiendre ceci mais avec vos adresses.

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

##  3. Scale the Whoami service to 3x
1. Open you Terminal window and change to the `01-Traefik-Overview` folder
2. Let's scale the `whoami` service to 3x instances by typing `docker-compose scale whoami=3`
3. Open a browser tab and paste `whoami.docker.localhost`  or from a terminal window `curl -H Host:whoami.docker.localhost http://127.0.0.1` and you should see the 3rd IP address update based on which service is responding.

##  4. Get to know the Traefik Dashboard
1. Open a browser tab and type or click: http://0.0.0.0:8080 to open the Traefik Dashboard


# Continue to the Configure Traefik Lab

### Click here to continue -> [Configure Traefik](https://github.com/56kcloud/traefik-training/blob/master/02-Configure-Traefik/traefik-configuration.md)