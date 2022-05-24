# Traefik Routers & Services

## 1. Deployer un routeur, un service, et un Load balancer
1. Avnat de commencer assurez-vous d'avoir stoppé tous les conteneurs du présent Labo `docker rm $(docker ps -a -q)` Cette commande prendra tout les conteneurs pour cible. 
2. Changez pour le dossier the `03-Traefik-Routeurs-&-Services`
3. Ouvez le fichier `docker-compose.file.yml` dans votre editeur de texte
4. A partir de ce dossier executer la commande -> `docker-compose up -d`
5. Verifier vos logs `docker-compose logs`
6. Accedez au dashboard Traefik et constatez les services qui tournent.

```yaml
  whoami:
    image: traefik/whoami
    labels:
       - "traefik.enable=true"
```

7. Dans le dossier  `03-Routers-and-Services` decommenter la section label comme ci-dessus
8. Accerder au dashboard Traefik  [http://0.0.0.0:8080](http://0.0.0.0:8080) et observer le routeur nouvellement créer `whoami`.

1. Toujours dans le dossier `03-Routers-and-Services`editer notre fichier compose `docker-compose.yml`et decommenter le reste des lignes afin de definir un routeur, un service et un point d'entrée. La nouvelle section de l'appli  `whoami` dans notre fichier doit resemble à cela :

```yaml
  whoami:
    image: traefik/whoami
     labels:
       - "traefik.enable=true"
       - "traefik.http.routers.whoami.rule=Host(`whoami.localhost`)"
       - "traefik.http.routers.whoami.entrypoints=web"
       - "traefik.http.routers.whoami.service=whoami"
```

10. Demarrer à nouveau la pile, avec la commande -> `docker-compose up -d`, les changement nécéssaires seront apporté.

## 3. Make everything Dynamic
In this Lab we will comment out the **Service** and **Load Balancer** Labels to see how Traefik will *Dynamically* create the service and **Load Balancer**.

1. From the `03-Routers-and-Services` directory edit the `docker-compose.yml` file and remove/add comment to the **Labels** `- "traefik.http.routers.catapp.service=catapp"` and `- "traefik.http.services.catapp.loadbalancer.server.port=5000"` from the `catapp`. The only **Labels** enabled as seen below:

```yaml
catapp:
    image: mikesir87/cats:1.0
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.catapp.rule=Host(`catapp.localhost`)"
      - "traefik.http.routers.catapp.entrypoints=web"
```

2. From the `03-Routers-and-Services` directory execute this command -> `docker stack deploy -c docker-compose.yml traefik` **this will update our Docker Swarm Stack with the new Label changes.**
3. Open the Traefik Dashboard [http://0.0.0.0:8080](http://0.0.0.0:8080) and wait about 15-20 seconds for the changes to be visible in the dashboard. 
4. Navigate to the **HTTP Router** & **HTTP Services** menus. You should now see that Traefik Dynamically created service name which is now recognized by the **Router**
5. Stop and clean-up `docker stack rm traefik`

### Solution for the catapp Labels Lab

Please check the `03-Routers-and-Services/docker-compose.answers.yml` if you get stuck anywhere during the lab or need a reference.

```yaml
catapp:
    image: mikesir87/cats:1.0
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.catapp.rule=Host(`catapp.localhost`)"
      - "traefik.http.routers.catapp.entrypoints=web"
      - "traefik.http.routers.catapp.service=catapp"
      - "traefik.http.services.catapp.loadbalancer.server.port=5000"
```

# Continue to the Next Lab HTTPS / TLS / Let's Encrypt Lab

### Click here to continue -> [HTTPS  & TLS](https://github.com/56kcloud/traefik-training/blob/master/04-HTTPS-TLS/traefik-https-tls.md)