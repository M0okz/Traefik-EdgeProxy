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

## 2. Comment les routeurs fonctionnent 

Vous pouvez définir sur chaque conteneur que vous souhaitez acheminer le trafic un  label. Le label "router" définit l’URL à laquelle le conteneur doit répondre, il peut en posséder plusieurs selon les besoins avec des conditions si nécéssaire
```
    labels: 
      - "traefik.http.routers.whoami.rule=Host(`whoami.localhost`)"
```
```
    labels#(Condition de couverture de tout un domaine): 
      - "traefik.http.routers.whoami.rule= Host(`whoami.localhost``) || Host(`www.whoami.localhost``)"
```

Vous pouvez également définir un port. Si votre conteneur fonctionne sur le port 8000, n’oubliez pas de le définir dans les label :
```
    labels: 
      - "traefik.http.routers.whoami.rule=Host(`whoami.localhost`)"
      - "traefik.http.services.whoami.loadbalancer.server.port=8000"
```
# Poursuivre vers la gestion du HTTPS / TLS  avec Let's Encrypt

### Clique ici -> [04-Traefik-HTTPS-&-TLS](https://github.com/M0okz/Traefik-Udemy/blob/main/04-Traefik-HTTPS-%26-TLS/traefik-https-tls.md)