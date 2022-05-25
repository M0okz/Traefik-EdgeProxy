# Traefik HTTPS / TLS / Let’s Encrypt Lab

# Pré-requis

** Pour réalise cet labo vous serez plus à l'aise en prenant un nom de domaine chez cloudfare pour exemple dans ce labo
Voici la liste des provider pris en charge par Traefik  [https://docs.traefik.io/v2.3/https/acme/#providers](https://docs.traefik.io/v2.3/https/acme/#providers)**

1. Assurez-vous d’avoir un nom de domaine
2. Pointer  un enregistrement sur votre ip publique de votre VPS ou de votre routeur.
3. Il y aura un sous dossier pour chaque méthode dans ce labo.
4. Voici l'extrait de notre Lab, pour cette section:


<img src="../img/cloudfare-onglet-dns.png" alt="Traefik DNS" height="250"> 


## 1. Deployer Traefik methode Let's Encrypt HTTP Challenge
1. Arretez et supprimer tout les conteneurs par cette commande : docker rm $(docker ps -a -q)
3. Ouvez le ficher `Docker-compose.yml`par votre editeur de text et par rapport à notre dernier LAB, une section à  été ajouter.
4. Verifier que l'adresse mail que vous renseigner est  joignable pour la validation
5. Edite le fichier `.env` et ajouter la racine de votre nom de domaine 
6. Lancer la pile `docker-compose up` afin d'observer les logs directement de la pile.
7. Via votre navigateur acceder au Dashboard Traefik [http://0.0.0.0:8080](http://0.0.0.0:8080)verifier ensuite que Traefik et nos services sont en HTTPS.
8. Vous devez normalement observer votre site whoami.xxxx.fr est bien servi avec HTTPS et sont propre certifcat Let's Encrypt

## 2. Deployer Traefik methode Let's Encrypt TLS Challenge
1. Arretez et supprimer tout les conteneurs par cette commande : docker rm $(docker ps -a -q)
2. Deplacez vous dans le dossier `Methode TLS` de notre labo `04-Traefik-HTTPS-&-TLS`
3. Ouvrez le fichier `Docker-compose.yml` a la ligne 24 le changement à été fait pour le `Challenge TLS`
4. Edite le fichier `.env` et ajouter la racine de votre nom de domaine 
5. Lancer la pile `docker-compose up` afin d'observer les logs directement de la pile.
6. Open the Traefik Dashboard `http://your_domain_here:8080` and verify Traefik is running and `catapp` has TLS enabled.
7. Via votre navigateur acceder au Dashboard Traefik [http://0.0.0.0:8080](http://0.0.0.0:8080)verifier ensuite que Traefik et nos services sont en HTTPS.
8. Vous devez normalement observer votre site whoami.xxxx.fr est bien servi avec HTTPS et sont propre certifcat Let's Encrypt

## 3. Deployer Traefik methode Let's Encrypt DNS Challenge
1. Arretez et supprimer tout les conteneurs par cette commande : docker rm $(docker ps -a -q)
2. Change to the `04-HTTPS-and-TLS` folder
3. Log in to your DNS provider and collect the Authorization Tokens for your provider. Review the [https://docs.traefik.io/v2.3/https/acme/#providers](https://docs.traefik.io/v2.3/https/acme/#providers) list to see which tokens you require for your provider. This step is unique to the DNS provider you are using. 
4. Pour ceux qui travaillent avec cloudfare voici un exemple de notre fichier `.env` avec les variables nécéssaires
````dosini
DOMAINNAME=trazcer.fr
CLOUDFLARE_ZONEID=c6595ef4ed7de97a94a3efaf05886
CLOUDFLARE_APITOKEN=-DlClDkZ-l_ELoIklaCK7AiH999U2vvDPhDOZ
CLOUDFLARE_EMAIL=gregory.narcin@icloud.com
````
5. Ensuite ouvrez le fichier `docker-compose.dns.yml` a la `ligne 24` dans la section pour le challenge ajouter le bloc ci-dessous :
````
      ##- --certificatesResolvers.dns-cloudflare.acme.caServer=https://acme-staging-v02.api.letsencrypt.org/directory 
      - --certificatesResolvers.dns-cloudflare.acme.email=$CLOUDFLARE_EMAIL
      - --certificatesResolvers.dns-cloudflare.acme.storage=./letsencrypt/acme.json
      - --certificatesResolvers.dns-cloudflare.acme.dnsChallenge.provider=cloudflare
      - --certificatesResolvers.dns-cloudflare.acme.dnsChallenge.resolvers=1.1.1.1:53,1.0.0.1:53
      - --certificatesResolvers.dns-cloudflare.acme.dnsChallenge.delayBeforeCheck=90
    environment:
      - CF_API_EMAIL=CLOUDFLARE_EMAIL
      - CF_API_KEY=$CLOUDFLARE_APITOKEN
````
6. 
7. Paste the Authorization Tokens from your provider in this section. You may need different and/or additional fields here based on your provider.

## Deployer Traefik avec un certificat.

```yml
    environment:
      - "DO_AUTH_TOKEN=<Your Super Secret Digital Ocean Token Goes here>"
```

8. In the same `docker-compose.dns.yml` edit the `catapp` section and add your domain here in the `- "traefik.http.routers.catapp.rule=Host(`your_domain_here`)"` label
9.  Open the `traefik.dns.yml` file in your favorite editor and review the `Challenge DNS` section
10. Edit the `traefik.dns.yml` and edit your `email:` located in the `Challenge DNS` section
11. Start Traefik and the `catapp` `docker stack deploy -c docker-compose.yml traefik`
12. Open the Traefik Dashboard `http://your_domain_here:8080` and verify Traefik is running and `catapp` has TLS enabled.
13. Open the `catapp` using the domain you filled in at step 6. Remember to use HTTPS now https://your_domain_here.com 
14. You should now see the `catapp` served with HTTPS and a proper Let's Encrypt Certificate

## 3.1 Use Wildcard Let's Encrypt Certificate

<img src="../img/traefik-dns-wildcard.png" alt="Traefik DNS" height="250"> 

1. Add a new DNS record to your DNS provider. Add `*.` in front of your domain `*.you_domain.com` which enables all sub-domain certificates
2. Now that the Wildcard is configured for DNS, we can edit the Edit the `docker-compose.dns.yml` `catapp` section and add your domain here in the `- "traefik.http.routers.catapp.rule=Host(`your_domain_here`)"` label. This time we will update the domain to `training.your_domain_here.com` 
3. Open up `https://training.your_domain_here`

# Continue to the Next Lab Middlewares

### Click here to continue -> [Middlewares Lab](https://github.com/56kcloud/traefik-training/blob/master/05-Middlewares/traefik-middlewares.md)