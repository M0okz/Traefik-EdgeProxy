# Traefik Logs & Monitoring Lab

## 1. Activer les logs de debog 
1. Arretez et supprimer tout les conteneurs par cette commande : docker rm $(docker ps -a -q)
2. Changer de répertoire : `06-Traefik-Monitoring-&-Logs`
3. Ouvez le ficher `Docker-compose.yml`par votre editeur de texte
4. Changez le niveau de loggin de `INFO` sur `DEBUG`

```yml
 - --log.level=DEBUG
```

5.  Lancer la pile `docker-compose up` afin d'observer les logs directement de la pile, traefik génére plus de logs maintenant
7.  Ctrl+C pour arreter l'execution, nous allons passer à nouveau sur un niveau `INFO`

```yml
 - --log.level=INFO
```

## 2. Enable Access Logs
1. Dans le répertoire : `06-Traefik-Monitoring-&-Logs`, vous avez observer un nouveau dossier `logs` qui servira à stocker les fichiers de logs. 
3. Ouvez le ficher `Docker-compose.yml`par votre editeur de texte
4. Ajouter l'argument dans la section `command` : 

```yml
      - --accesslog=true
      - --accesslog.filepath=logs/traefik.log
```

5. Et decommenter le volume `logs` :

```yml
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./letsencrypt:/letsencrypt
      - ./config:/config
      - ./logs:/logs
```

5. Lancer la pile `docker-compose up -d`  
6. Acceder à l'application `whoami` dans un nouvel onglet  [https://whoami.your_domain_here](https://whoami.your_domain_here) et actualiser la page plusieurs fois
7. Dans le dossier `logs` un fichier `traefik.log` contien maintenant toutes les entrée logs de traefik. 
8. Nous allons filtrer pour ne voir que les codes `404`. Editez le ficjhier `Docker-compose.yml` et dans la section `command` ajouter l'argument :
   
```yml
- --accessLog.filters.statusCodes=404
```

9. Arretez et supprimer tout les conteneurs par cette commande : docker rm $(docker ps -a -q)
10. Supprimer le fichier `traefik.log` dans le dossier `logs`
11. Lancer la pile `docker-compose up -d`
10. Acceder à l'application `whoami` dans un nouvel onglet  [https://whoami.your_domain_here](https://whoami.your_domain_here) et actualiser la page plusieurs fois
11. Ouvez le fichier `traefik.log`, aucune entrée n'est ajouter pour l'instant.
12. Créer une erreur `404` en accedant à une url avec un repertoir inexistant :  [https://whoami.your_domain_here/broken](https://whoami.your_domain_here/broken)
13.  Ouvez le fichier `traefik.log`, vous devez retrouver maintenant des entrée uniquement des erreurs provoqué plus haut.

## 3. Enable Traefik Metrics
1. Before we begin, lets cleanup the HTTP stack  `docker stack rm traefik` If you named you stack something else use your specified name. If you don't remember run `docker stack ls`
2. Change to the `06-Observability` folder
3. Open the `traefik.metrics.yml` file in your favorite editor and review the `Prometheus Metrics` section. The buckets Prometheus metrics buckets have been enabled for the different time series in seconds.

```yml
metrics:
  prometheus:
    buckets:
      - 0.1
      - 0.3
      - 1.2
      - 5.0
```
4. Start Traefik with `Metrics` enabled `docker stack deploy -c docker-compose.metrics.yml traefik`

**NOTE: This will take a couple minutes as Prometheus and Grafana need to download and start** 

5. Login to Promethes [prometheus.localhost](http://prometheus.localhost)
6. Review the available metrics imported into Prometheus by opening Graph and then click the dropdown and scroll to the bottom to find Traefik metrics
7. Login to Grafana [grafana.localhost](http://grafana.localhost)
8. Grafana user is `admin` and password is `foobar`
9. Open the Dashboard `Traefik2`
10. Visualize the Stats from Traefik

# Continue to the Next Operations

### Click here to continue -> [Operations Lab](https://github.com/56kcloud/traefik-training/blob/master/07-Operations/traefik-operations.md)