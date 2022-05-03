# Getting Started with traefik

Dans ce dossier vous retrouver les ressources pour nos premiers pas avec Traefik. 
Pour rouler un container Traefik:
1. Soyez positionner dans ce dossier
2. Lancer un `docker-compose up`

A preésent traefik est lancé.
Dans un navigateur saisissez `localhost:8080` pour acceder au tableau de bord.

Dans un autre onglet : `whoami.localhost`avec le navigateur. Vous obtiender un page de réposne du serveur web..

You can also play around by scaling the containers of whoami service up by running 
```
docker-compose up --scale whoami=3
```
Now open `whoami.localhost` again and refresh the page a couple of times. You will see that the hostname values are changing. 
Traefik routes the requests and forwards them to any whoami container running.

## How to configure traefik
There are multiple ways to configure traefik.
In the `docker-compose.yml` we have 2 ways to set the traefik settings:
### Via commands:
```
services:
  reverse-proxy: 
    image: traefik:v2.4
    command: --api.insecure=true --providers.docker
```
This is recommended to just try traefik out.

### Traefik.yml
This is the preferred way to configure traefik. 
to activate the traefik.yml, mount it to the following directory:
```
    volumes:
      - ./traefik.yml:/etc/traefik/traefik.yml
```