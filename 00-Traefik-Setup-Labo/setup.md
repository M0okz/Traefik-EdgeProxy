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

2. Once Docker is installed, let's verify the docker installation. Open a terminal window (Windows users open Powershell with Admin rights). Next, run the following command

```docker
    docker run hello-world
```

3. If the command completes successfully you should see a hello message from Docker. If the command failed, be sure to revisit the step 1 instructions on installing Docker for Desktop for your Operating System.

4. Install Git, so we can check out the Labs - https://git-scm.com/downloads or GitHub Desktop https://desktop.github.com/

What if Docker Desktop doesn't work?
In some cases, Docker Desktop will not work due to the version of your operating system of IT policies in your company. Don't worry, we have a solution for this as well. 

You can use the online Docker console provided by Docker. This works entirely in your browser - https://labs.play-with-docker.com/

Check out the Labs
Now, let's check out the Labs which contain all the resources for each lab. From your terminal window, Git client, GitHub Desktop, or Visual Studio code clone the Traefik Training Repo.

```bash 
git clone https://github.com/56kcloud/traefik-training.git
```

You can find the Traefik Training GitHub Repo here - https://github.com/56kcloud/traefik-training

## Questions
If you have a question or need help, be sure to ask a question in the comments or reach out to our support team.

# Continue to the Traefik Overview Lab

### Click here to continue -> [Configure Traefik](https://github.com/56kcloud/traefik-training/blob/master/01-Traefik-Overview/traefik_overview.md)