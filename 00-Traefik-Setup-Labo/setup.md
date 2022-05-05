# Configuration de notre LABO

<img src="../img/Traefik_training.png" alt="Traefik Logo" height="350"> 

## LAB Prerequisites

1. Installation docker pour Linux, Mac ou Windows.

**Linux** - Installer votre distribution favorite  : Debian, Ubuntu.. (Exemple pour debian 11)
```docker
    sudo apt update

    sudo apt install apt-transport-https ca-certificates curl gnupg lsb-release -y

    curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/debian \
    $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

    sudo apt update && sudo apt -y install docker-ce docker-ce-cli containerd.io

    sudo systemctl status docker

    sudo systemctl enable docker

    sudo usermod -aG docker $User(votre utilisateur non root)

    sudo curl -L https://github.com/docker/compose/releases/download/v2.5.0/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose

    sudo chmod +x /usr/local/bin/docker-compose

    docker-compose -v
```

**Windows** - Pour ceux qui souhaite il est possible d'executer docker sous windows :https://docs.docker.com/docker-for-windows/install/

Pensez à switch sur les container Linux : https://docs.docker.com/docker-for-windows/#switch-between-windows-and-linux-containers

**Mac** - https://docs.docker.com/docker-for-mac/install/

2. Maintenant que docker est installer nous allons verifier que tout roule parfaitement. Dans un terminal (Powershell pour les WindowsUsers), saisissez la commande suivant pour lancer un container "test"

```docker
    docker run hello-world
```

3. Si cette commande retourne un message Hello de Docker tout est bon pour cette partie. Sinon reporter au meesage d'erreur recontrer et refaite les étape plus haut.

4. Installer les outils supplémentaire tel que GIt et Vscode, il existe des version pour Windows, Mac et Linux.

Maintnenant récuperer tout le contenu de la formation avec votre client Git.
```bash 
git clone https://github.com/M0okz/Traefik-Udemy.git
```

Retrouver le Repo GitHub de la formation - https://github.com/M0okz/Traefik-Udemy

## Questions
Si vous avez des questions ou des problématique sur cette étape, venez rejooindre le Discord prévu. https://discord.gg/sswhANDbKa .

# Continuer pour un Aperçus de Traefik

### Clique ici -> [01-Traefik-Aperçu](https://github.com/56kcloud/traefik-training/blob/master/01-Traefik-Overview/traefik_overview.md)