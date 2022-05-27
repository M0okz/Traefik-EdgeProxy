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

Dans la section `command` du fichier `Docker-compose.yml` nous allons passer cet argument supplémentaire : `- --providers.file.directory=./config`
Puis nous allons déclarer ce dossier `config` dans la section `volumes` : `- ./config:/config`
Voilà mainteant Traefik est prêt à allez lire tout nos fichiers de configurations à chaud contenu dans ce dossier.

## 1. Middleware Basic Auth
1. Commence par, arretez et supprimer tout les conteneurs par cette commande : docker rm $(docker ps -a -q)
2. Deplacez vous dans le dossier `05-Traefik-Modules`.
3. Sur votre serveur penser à installer les utilitaires Apache : `sudo apt install apache2-utils`
4. Générez un nouveau mot de passe pour notre appli `whoami` par cette commande `echo $(htpasswd -nb traefik whoami) | sed -e s/\\$/\\$\\$/g`
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
      users=traefik:$$apr1$$.zPbdVg8$$LcHeyCZElH.JfxkxxlMPI"
```
4. Creer un nouveau router qui s'occupera de géré nos differents middlewares `- "traefik.http.routers.whoami.middlewares=whoami-basicauth"`
5. Avant de lancer la pile, vous pouvez recuperer le certificat `acme.json` de notre précédant labo en le copiant dans le même dossier sur ce labo (chmod 600 ensuite)
8. Lancer la pile `docker-compose up -d`
9. Dashboard Traefik  `http://your_domain_here:8080` et verifier la création de notre middleware `whoami-basicauth` est lancé et assigné à notre service `whoami`
10. Open the `whoami` application in a new browser tab [http://whoami.localhost](http://whoami.localhost)
11. Enter the user `traefik` and password `training` to visit your `whoami` application

## 2. Add Compression Middleware to our whoami
1. Before we begin, lets cleanup the HTTP stack  `docker stack rm traefik` If you named you stack something else use your specified name. If you don't remember run `docker stack ls`
2. Change to the `05-HTTPS-and-TLS` folder
3. Open the `docker-compose.compress.yml` file in your favorite editor and review the `whoami` section
4. Add the **Compress Middleware** to our `whoami` section `- "traefik.http.middlewares.test-compress.compress=true"`
5. Update the router to include the **Compress Middleware** ` - "traefik.http.routers.whoami.middlewares=test-auth,test-compress"`
6. Start Traefik and the `whoami` `docker stack deploy -c docker-compose.compress.yml traefik`
7. Open the Traefik Dashboard [http://0.0.0.0:8080](http://0.0.0.0:8080) and verify the new `test-compress` **Middleware** is running and and assigned to the `whoami` service
8.  Open the `whoami` application in a new browser tab [http://whoami.localhost](http://whoami.localhost)

## 3. Add Error Pages Middleware
1. Before we begin, lets cleanup the HTTP stack  `docker stack rm traefik` If you named you stack something else use your specified name. If you don't remember run `docker stack ls`
2. Change to the `05-HTTPS-and-TLS` folder
3. Open the `docker-compose.error.yml` file in your favorite editor and review the `whoami` section
4. Add the Error Page service below the `whoami`. We are using the [Traefik Custom Error Page service](https://github.com/guillaumebriday/traefik-custom-error-pages) check out the Repo for more details how to modify the Error pages.

  ```yaml
  # Error Page service
    error:
      image: guillaumebriday/traefik-custom-error-pages
      labels:
            - "traefik.enable=true"
            - "traefik.http.routers.error.rule=Host(`error.localhost`)"
            - "traefik.http.routers.error.service=error"
            - "traefik.http.services.error.loadbalancer.server.port=80"
            - "traefik.http.routers.error.entrypoints=web"
  ```

4. Add the **Error Page Middleware** to our `whoami` section

```yaml
# Error Pages Middleware
       - "traefik.http.middlewares.test-errorpages.errors.status=400-599"
       - "traefik.http.middlewares.test-errorpages.errors.service=error"
       - "traefik.http.middlewares.test-errorpages.errors.query=/{status}.html"
```


5. Update the router to include the **Error Page middleware** ` - "traefik.http.routers.whoami.middlewares=test-auth,test-compress,test-errorpages"`
6. Start Traefik and the `whoami` `docker stack deploy -c docker-compose.error.yml traefik`
7. Open the Traefik Dashboard [http://0.0.0.0:8080](http://0.0.0.0:8080) and verify the new `test-errorpages` **Middleware** is running and and assigned to the `whoami` service
8.  Open the `whoami` application in a new browser tab [http://whoami.localhost](http://whoami.localhost)
9.  Let's produce a 404 error to see our error page in actions. Open a new browser tab [http://whoami.localhost/broken](http://whoami.localhost/broken)

## 4. Add Rate Limit Middleware
1. Before we begin, lets cleanup the HTTP stack  `docker stack rm traefik` If you named you stack something else use your specified name. If you don't remember run `docker stack ls`
2. Change to the `05-HTTPS-and-TLS` folder
3. Open the `docker-compose.ratelimit.yml` file in your favorite editor and review the `whoami` section
4. Add the **Rate Limit Middleware** to our `whoami` section `- "traefik.http.middlewares.test-ratelimit.ratelimit.average=2"`
5. Update the router to include the **Rate Limit middleware** ` - "traefik.http.routers.whoami.middlewares=test-auth,test-compress,test-ratelimit"`
6. Start Traefik and the `whoami` `docker stack deploy -c docker-compose.ratelimit.yml traefik`
7. Open the Traefik Dashboard [http://0.0.0.0:8080](http://0.0.0.0:8080) and verify the new `test-ratelimit` **Middleware** is running and and assigned to the `whoami` service
8. Open the `whoami` application in a new browser tab [http://whoami.localhost](http://whoami.localhost)
9. Refresh the `whoami` page quickly to see the Rate Limit error

## 5. Add Redirect Middleware

**This section we need your DNS settings again from Section `04-HTTPS-TLS` We will use the DNS settings to test the HTTP -> HTTPS redirect. Ensure your DNS settings are configured in the `docker-compose.redirect.yml`**

1. Before we begin, lets cleanup the HTTP stack  `docker stack rm traefik` If you named you stack something else use your specified name. If you don't remember run `docker stack ls`
2. Change to the `05-HTTPS-and-TLS` folder
3. Open the `docker-compose.redirect.yml` file in your favorite editor and review the `whoami` section
4. Next, we need to seperate the two entrypoints for HTTP and HTTPS. First, we relable HTTP
   

```yaml
  # Routers
       - "traefik.http.routers.whoami.rule=Host(`<your-domain-here>`)"
       - "traefik.http.routers.whoami.entrypoints=web"
       - "traefik.http.routers.whoami.middlewares=redirect"
``` 
5. Relabel the entrypoint labels for HTTPS using `whoami-secure`

```yaml
       - "traefik.http.routers.whoami-secure.rule=Host(`<your-domain-here>`)"
       - "traefik.http.routers.whoami-secure.entrypoints=websecure"
       - "traefik.http.routers.whoami-secure.tls.certresolver=myresolver"
       - "traefik.http.routers.whoami-secure.middlewares=test-auth,test-compress,test-errorpages,test-ratelimit"
```

6. Add the **Redirect Scheme middleware** to our `whoami` section

  ```yaml
        - "traefik.http.middlewares.test-redirectscheme.redirectscheme.scheme=https"
        - "traefik.http.middlewares.test-redirectscheme.redirectscheme.permanent=true"
  ```

7. Update your domain name in `- "traefik.http.routers.whoami.rule=Host(`<your-domain-here>`)"` and here `- "traefik.http.routers.whoami-secure.rule=Host(`<your-domain-here>`)"`
8. Add your DNS tokens to the Enviornment section of Traefik
9. Start Traefik and the `whoami` `docker stack deploy -c docker-compose.redirect.yml traefik`
10. Open the Traefik Dashboard [http://0.0.0.0:8080](http://0.0.0.0:8080) and verify the new `test-redirectscheme` **Middleware** is running and and assigned to the `whoami` service
11. Open the `whoami` application in a new browser tab and open `your-domain` configured in the DNS section
12. You should see your `whoami` domain redirect from HTTP -> HTTPS automagically. 

# Continue to the Next Observability

### Click here to continue -> [Observability Lab](https://github.com/56kcloud/traefik-training/blob/master/06-Observability/traefik-observability.md)