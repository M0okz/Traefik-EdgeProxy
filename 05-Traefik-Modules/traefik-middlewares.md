# Traefik Middlewares Lab


# Middlewares

Connecté aux routeurs, les middlewares sont un moyen de peaufiner les demandes avant qu’elles ne soient envoyées à vos services (ou avant que la réponse des services ne soit envoyée aux clients)

Ce labo vous montreras :
* Comment utiliser les middlewares pour changer, manipuler ou ajuster les requetes.
* Comment activer pour vos webservices-microservices.

## 0. Traefik configuration fichiers dynamiques
Avant de débuter ce labo, pour plus de clareté et de simplicité dans la gestion de nos ocnfiguration,
il est préférable d'ajouter quelques lignes qui permettron à Traefik d'avoir un emplacement ou charger tout nos 
futur fichiers de configurations.

Dans la section `command` du fichier `Docker-compose.yml` nous allons ajouter cet argument : `- --providers.file.directory=./config`
Puis nous allons déclarer ce dossier `config` dans la section `volumes` : `- ./config:/config`
Voilà mainteant Traefik est prêt à allez lire tout nos fichiers de configurations à chaud contenu dans ce dossier.

## 1. Middleware Basic Auth
1. Commence par, arretez et supprimer tout les conteneurs par cette commande : docker rm $(docker ps -a -q)
2. Deplacez vous dans le dossier `05-Traefik-Modules`.
3. Sur votre serveur penser à installer les utilitaires Apache : `sudo apt install apache2-utils`
4. Générez un nouveau mot de passe, ici  `whoami` avec l'utilisateur `traefik` : `echo $(htpasswd -nb traefik whoami) | sed -e s/\\$/\\$\\$/g`
5. Tout les `$` dans la chaine de caratère doivent être doublés `$$` pour échaper les caractères correctement. 

Lancer la commande `htpasswd`

```bash
echo $(htpasswd -nb traefik whoami) | sed -e s/\\$/\\$\\$/g
traefik:$$apr1$$.zPbdVg8$$LcHeyCZElH.JfxkxxlMPI.

```
2. Créer le ficher `middlewares.toml` dans le dossier `config` avec votre editeur de texte.
3. Ajouter le middleware basicAuth avec le bloc suivant :
```toml
[http.middlewares]
    [http.middlewares.whoami-basicauth.basicAuth]
      users = ["traefik:$$apr1$$.zPbdVg8$$LcHeyCZElH.JfxkxxlMPI"]
```
4. Ouvez le fichier `Docker-compose.yml` et creer un nouveau router qui s'occupera de géré nos differents middlewares `- "traefik.http.routers.whoami.middlewares=whoami-basicauth"`
5. Avant de lancer la pile, vous pouvez recuperer le certificat `acme.json` de notre précédant labo en le copiant dans le même dossier sur ce labo (chmod 600 ensuite)
8. Lancer la pile `docker-compose up -d`
9. Dashboard Traefik  `https://your_domain_here:8080` et verifier la création de notre middleware `whoami-basicauth` est lancé et assigné à notre service `whoami`
10. Accéder à notre application [https://whoami.your_domain_here](https://whoami.your_domain_here)
11. Saisissez l'utilisateur `traefik`  et le mot de passe `whoami` pour acceder à notre application `whoami`

## 2. Middleware Compression
1. Editez le fichier `middlewares.toml` pour ajouter le middleware `whoami-compress` : 
````toml
[http.middlewares]
    [http.middlewares.whoami-basicauth.basicAuth]
      users = ["traefik:$$apr1$$.zPbdVg8$$LcHeyCZElH.JfxkxxlMPI"]
    [http.middlewares.whoami-compress.compress]
````
2. Ouvez le fichier `Docker-compose.yml` et ajouter le middleware à la suite  : `traefik.http.routers.whoami.middlewares=whoami-basicauth,whoami-compress`
3. Recharger la pile `docker-compose up -d` traefik detectera les changements apportés.
4. Accéder à notre application [https://whoami.your_domain_here](https://whoami.your_domain_here) et verifier que le middleware est créer `whoami-compress` et  assigné à notre application

## 3. Middleware page d'erreurs
1. Editez le fichier `middlewares.toml` pour ajouter le middleware `whoami-errorpage` : 
````toml
[http.middlewares]
    [http.middlewares.whoami-basicauth.basicAuth]
      users = ["traefik:$$apr1$$.zPbdVg8$$LcHeyCZElH.JfxkxxlMPI"]
    [http.middlewares.whoami-compress.compress]
    [http.middlewares.whoami-errorpage.errors]
      status = ["400-599"]
      service = "htttperror"
      query = "/{status}.html"
````
4. Ajouter la pag d'erreur a notre service `whoami`. Nous allons utiliser le [Traefik Custom Error Page service](https://github.com/guillaumebriday/traefik-custom-error-pages) allez voir le Repo pour plus de détails (Pensez à céer une entré DNS pour ce nouveau service).
  ```yaml
  # Error Page service
    error:
      image: tarampampam/error-pages
      environment:
        TEMPLATE_NAME: app-down
      labels:
            - "traefik.enable=true"
            - "traefik.http.routers.error.rule=Host(`error.$DOMAINNAME`)"
            - "traefik.http.routers.error.service=htttperror"
            - "traefik.http.services.error.loadbalancer.server.port=8080"
            - "traefik.http.routers.error.entrypoints=web"
      depends_on:
      - traefik
  ```
4. Ouvez le fichier `Docker-compose.yml` et ajouter le middleware à la suite : `traefik.http.routers.whoami.middlewares=whoami-basicauth,whoami-compress,whoami-errorpage`
6. Recharger la pile `docker-compose up -d` traefik detectera les changements apportés.
4. Accéder à notre application [https://whoami.your_domain_here](https://whoami.your_domain_here) et verifier que le middleware est créer `whoami-compress` et  assigné à notre application
8.  Acceder à l'application `whoami` dans un nouvel onglet  [https://whoami.your_domain_here](https://whoami.your_domain_here)
9.  Nous allons produire une erreur. Ouvre un nouvel onglet et ajouter :  [https://whoami.your_domain_here/broken](https://whoami.your_domain_here/broken)

## 4. Middleware redirection https
1. Ouvez le fichier `Docker-compose.yml` et ajouter le middleware directement à Traefik qui passerera toutes les requetes http -> htttps
2. Ajouter le bloc suivant dans notre service `reverse-proxy` : 
````yml
    labels:
      traefik.enable: true
      ## HTTP-to-HTTPS Redirect
      traefik.http.routers.http-catchall.rule: hostregexp(`{host:.+}`)
      traefik.http.routers.http-catchall.entrypoints: web
      traefik.http.routers.http-catchall.middlewares: redirect-to-https
      traefik.http.middlewares.redirect-to-https.redirectscheme.scheme: https
````
3. Nous avons aussi créer un routeur pour recuperer toutes les request sur le point d'entré `web` pour les passé au middleware `redirectscheme`
6. Recharger la pile `docker-compose up -d` traefik detectera les changements apportés.
10. Open the Traefik Dashboard [http://0.0.0.0:8080](http://0.0.0.0:8080) and verify the new `test-redirectscheme` **Middleware** is running and and assigned to the `whoami` service
8. Acceder à l'application `whoami` dans un nouvel onglet  [https://whoami.your_domain_here](https://whoami.your_domain_here)
12. Nous allons tester la redirection, Ouvrez un nouvel onglet en navigation privée et saisssez l'url de l'appli en http et observer la redirection automatique HTTP -> HTTPS. 

## 5. Middleware chaines
Les chaines de middlewares vous permettent de définir des combinaisons réutilisables pour tout vos services. Cela facilite la réutilisation des mêmes chaines.
1. Créer un nouveau fichier `middlewares-chains.toml` dans le dossier `config` du dossier `05-Traefik-Modules` 
2. Ajouter à ce fichier le bloc suivant en exemple pour notre application : 
````toml
  [http.middlewares.chain-basic-auth]
    [http.middlewares.chain-basic-auth.chain]
      middlewares = [ "whoami-basicauth", "whoami-compress"]
````
## Poursuivre vers le monitoring et la gestion des logs

### Click here to continue -> [Observability Lab](https://github.com/56kcloud/traefik-training/blob/master/06-Observability/traefik-observability.md)