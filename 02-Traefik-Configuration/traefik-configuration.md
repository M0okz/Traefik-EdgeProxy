# Configure Traefik

## 1. Configuration statique CLI
1. Assurez vous que tout les conteneur précédent soit eteints avec ``docker ps``. Si ce n'est pas le cas dans le précédent dossier faite un `docker-compose stop`
2. Dans le dossier `02-Traefik-Configuration`
3. Ouvez le fichier `docker-compose.file.yml` dans votre editeur de texte
4. A partir de ce dossier executer la commande -> `docker-compose up -d`
5. Verifier vos logs `docker-compose logs`
6. Arreter et effacer tout les conteneurs `docker-compose  stop`

```yml
services:
#########################################################
## TRAEFIK
#########################################################
  reverse-proxy: 
    container_name: traefik
    image: traefik:v2.6.3
    ports:
      - 80:80
      - 8080:8080 #Management UI
    command: # CLI arguments
      - --entrypoints.web.address=:80      
      - --entrypoints.websecure.address=:443
      - --api.dashboard=true
      - --providers.docker=true
      - --providers.docker.exposedByDefault=false
      - --log.level=INFO # (Default: error) DEBUG, INFO, WARN, ERROR, FATAL, PANIC
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
#      - ./traefik.yml:/etc/traefik/traefik.yml
```
### 2. Fichier de configuration statique

1. Ouvrez le fichier `traefik_conf.yml` vous trouverez la configuration ci-dessous dans un fichiers à part à l'inverse de la présentation ci-dessus.

```yaml
################################################################
# API and dashboard configuration
################################################################
api:
  # Dashboard
  #
  #
  dashboard: true
################################################################
# Docker configuration provider
################################################################
providers:
  docker: 
    watch: true
    exposedByDefault: false
################################################################
# Traefik Logging
################################################################
log:
  level: INFO
```
2. Decommenter la configration statique CLI comme ceci, dans le fichier `docker-compose.yml`

```yaml
services:
#########################################################
## TRAEFIK
#########################################################
  reverse-proxy: 
    container_name: traefik
    image: traefik:v2.6.3
    ports:
      - 80:80
      - 8080:8080 #Management UI
    command: # CLI arguments
#      - --entrypoints.web.address=:80      
#      - --entrypoints.websecure.address=:443
#      - --api.dashboard=true
#      - --providers.docker=true
#      - --providers.docker.exposedByDefault=false
#      - --log.level=INFO # (Default: error) DEBUG, INFO, WARN, ERROR, FATAL, PANIC
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./traefik.yml:/etc/traefik/traefik.yml
```
# Continuer vers la Gestion des Routers & Services

### Clique ici -> [03-Traefik-Routeurs-&-Services](https://github.com/M0okz/Traefik-Udemy/blob/main/03-Traefik-Routeurs-%26-Services/traefik-routers-and-services.md)