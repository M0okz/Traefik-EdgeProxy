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
## 4. Static Configuration Entrypoint Lab
In this section, we will start using Docker Swarm. Ensure you have followed the Prerequisites from the Lab Setup. This can be found in the Getting Started Section of the course if you need to revisit. If you have any issues please add a comment in the course.

## NOTE about Docker ##
We use the `docker stack deploy -c <compose file name> <user provided stack name>` command to deploy Swarm services. `docker stack ps <stack name>` is used to check if all services are running in the stack. Finally, `docker stack rm <stack name>` removes the entire stack. 

To check logs on a running service, `docker service ls` to retrieve the name of the service and `docker service logs <service name>` to vier it's logs. 

Further Docker information can be found here for [Docker Stack](https://docs.docker.com/engine/reference/commandline/stack/) or [Docker services](https://docs.docker.com/engine/reference/commandline/service/)

1. Open the `traefik-entrypoints.yml` and `docker-compose.configuration.yml` files in your favorite editor and review how Entrypoints are created and how we are using the YML file in this Lab.
2. From the `02-Configure-Traefik` directory execute this command -> `docker stack deploy -c docker-compose.configuration.yml catapp`
3. Open the Traefik Dashboard [http://0.0.0.0:8080](http://0.0.0.0:8080) and review how the test service `catapp` is configured with **Entrypoints**.
4. Review the `catapp@docker` router and `catapp@docker` service in the Traefik Dashboard
5. Test the newly deployed `catapp` service [http://catapp.localhost/](http://catapp.localhost/)
6. Stop and clean-up `docker stack rm catapp`


# Continue to the Next Lab Routers & Services

### Click here to continue -> [Routers & Services Lab](https://github.com/56kcloud/traefik-training/blob/master/03-Routers-and-Services/traefik-routers-and-services.md)