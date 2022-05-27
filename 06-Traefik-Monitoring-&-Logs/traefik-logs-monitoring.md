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
1. Avant de commencer, arretez et supprimer tout les conteneurs par cette commande : docker rm $(docker ps -a -q)
2. Changer de répertoire : `06-Traefik-Monitoring-&-Logs`
3.  Ouvez le ficher `Docker-compose.yml`par votre editeur de texte : 

Decommentez les lignes dans le service `reverse-proxy`
```yml
- --metrics.prometheus=true
- --metrics.prometheus.buckets=0.1,0.3,1.2,5.0
```

Ajouter ensuite les services suivants :

````yml
  prometheus:
    image: prom/prometheus:v2.22.1
    networks:
      - traefik
    volumes:
      - ./prometheus/:/etc/prometheus/
      - prometheus_data:/prometheus
    command:
      - '--config.file=/etc/prometheus/prometheus.yml'
      - '--storage.tsdb.path=/prometheus'
      - '--web.console.libraries=/usr/share/prometheus/console_libraries'
      - '--web.console.templates=/usr/share/prometheus/consoles'
    deploy:
      labels:
       - "traefik.http.routers.prometheus.rule=Host(`prometheus.localhost`)"
       - "traefik.http.routers.prometheus.service=prometheus"
       - "traefik.http.routers.prometheus.entrypoints=websecure"
       - "traefik.http.services.prometheus.loadbalancer.server.port=9090"
       - "traefik.docker.network=traefik"
      restart_policy:
        condition: on-failure
````

````yml
  grafana:
    image: grafana/grafana:7.3.1
    networks:
      - traefik
    depends_on:
      - prometheus
    volumes:
      - grafana_data:/var/lib/grafana
      - ./grafana/provisioning/:/etc/grafana/provisioning/
    env_file:
      - ./grafana/config.monitoring
      labels:
        - "traefik.http.routers.grafana.rule=Host(`grafana.localhost`)"
        - "traefik.http.routers.grafana.service=grafana"
        - "traefik.http.routers.grafana.entrypoints=web"
        - "traefik.http.services.grafana.loadbalancer.server.port=3000"
        - "traefik.docker.network=inbound"
      restart_policy:
        condition: on-failure

````
4. Lancer la pile `docker-compose up -d`

**NOTE: Le déploiement est plus longs avec les nouveau services à déployer** 

5. Ajouter les entrés DNS nécéssaires pour prometheus et graphana
5. Connecter vous à prometheus [prometheus.your_domain_here](http://prometheus.your_domain_here)
6. Passez en revue les métriques disponibles importées dans Prometheus en ouvrant les Graphiques puis cliquez sur le menu déroulant et faites défiler vers le bas pour trouver les métriques Traefik
7. Connecter vous ensuite à Grafana [grafana.your_domain_here](http://grafana.your_domain_here)
8. L'utilisateur Grafana est user is `traefik` and password is `mookfoo`
9. Ouvez le Dashboard `Traefik2`
10. Visualisez ensuite les beaux graphiques

# Continue to the Next Operations

### Click here to continue -> [Operations Lab](https://github.com/56kcloud/traefik-training/blob/master/07-Operations/traefik-operations.md)