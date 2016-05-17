<properties
   pageTitle="Gestione di contenitori del servizio contenitore di Azure tramite Docker Swarm | Microsoft Azure"
   description="Distribuire contenitori in Docker Swarm nel servizio contenitore di Azure"
   services="container-service"
   documentationCenter=""
   authors="neilpeterson"
   manager="timlt"
   editor=""
   tags="acs, azure-container-service"
   keywords="Docker, Contenitori, Micro-servizi, Mesos, Azure"/>

<tags
   ms.service="container-service"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/13/2016"
   ms.author="nepeters"/>

# Gestione dei contenitori con Docker Swarm

Docker Swarm offre un ambiente per la distribuzione di carichi di lavoro in contenitori in un set di host Docker in pool. Docker Swarm usa l'API Docker nativa. Il flusso di lavoro per la gestione dei contenitori in Docker Swarm è quasi identico a quello di un host con un singolo contenitore. Questo documento fornisce semplici esempi di distribuzione di carichi di lavoro in contenitori, in un'istanza del servizio contenitore di Azure di Docker Swarm. Per una documentazione più dettagliata su Docker Swarm, vedere [Docker Swarm in Docker.com](https://docs.docker.com/swarm/).

Prerequisiti per gli esercizi in questo documento:

[Creare un cluster Swarm nel servizio contenitore di Azure](./container-service-deployment.md)

[Connettersi a un cluster Swarm nel servizio contenitore di Azure](./container-service-connect.md)

## Distribuire un nuovo contenitore

Per creare un nuovo contenitore in Docker Swarm, usare il comando `docker run`. Questo esempio consente di creare un contenitore dall'immagine `yeasy/simple-web`:


```bash
user@ubuntu:~$ docker run -d -p 80:80 yeasy/simple-web

4298d397b9ab6f37e2d1978ef3c8c1537c938e98a8bf096ff00def2eab04bf72
```

Dopo aver creato il contenitore, usare `docker ps` per restituire informazioni sul contenitore. Viene elencato l'agente Swarm che ospita il contenitore:


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   31 seconds ago      Up 9 seconds        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

Ora è possibile accedere all'applicazione in esecuzione in questo contenitore tramite il nome DNS pubblico del servizio di bilanciamento del carico dell'agente Swarm. Queste informazioni sono disponibili nel portale di Azure:


![Risultati della visita reali](media/real-visit.jpg)

## Distribuire più contenitori

Poiché nel cluster Docker Swarm vengono avviati più contenitori, è possibile usare il comando `docker ps` per vedere in quali host vengono eseguiti i contenitori. In questo esempio tre contenitori sono distribuiti uniformemente nei tre agenti Swarm:


```bash
user@ubuntu:~$ docker ps

CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                 NAMES
11be062ff602        yeasy/simple-web    "/bin/sh -c 'python i"   11 seconds ago      Up 10 seconds       10.0.0.6:83->80/tcp   swarm-agent-34A73819-2/clever_banach
1ff421554c50        yeasy/simple-web    "/bin/sh -c 'python i"   49 seconds ago      Up 48 seconds       10.0.0.4:82->80/tcp   swarm-agent-34A73819-0/stupefied_ride
4298d397b9ab        yeasy/simple-web    "/bin/sh -c 'python i"   2 minutes ago       Up 2 minutes        10.0.0.5:80->80/tcp   swarm-agent-34A73819-1/happy_allen
```  

## Distribuire contenitori con Docker Compose

È possibile usare Docker Compose per l'automazione della distribuzione e la configurazione di più contenitori. A questo scopo, assicurarsi che sia stato creato un tunnel SSH (Secure Shell) e che sia stata impostata la variabile DOCKER\_HOST.

Creare un file docker-compose.yml nel sistema locale. A questo scopo, usare questo [esempio](https://raw.githubusercontent.com/rgardler/AzureDevTestDeploy/master/docker-compose.yml).

```bash
web:
  image: adtd/web:0.1
  ports:
    - "80:80"
  links:
    - rest:rest-demo-azure.marathon.mesos
rest:
  image: adtd/rest:0.1
  ports:
    - "8080:8080"

```

Eseguire `docker-compose up -d` per avviare le distribuzioni dei contenitori:


```bash
user@ubuntu:~/compose$ docker-compose up -d
Pulling rest (adtd/rest:0.1)...
swarm-agent-3B7093B8-0: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/rest:0.1... : downloaded
swarm-agent-3B7093B8-3: Pulling adtd/rest:0.1... : downloaded
Creating compose_rest_1
Pulling web (adtd/web:0.1)...
swarm-agent-3B7093B8-3: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-0: Pulling adtd/web:0.1... : downloaded
swarm-agent-3B7093B8-2: Pulling adtd/web:0.1... : downloaded
Creating compose_web_1
```

Infine, verrà restituito l'elenco dei contenitori in esecuzione. Questo elenco riflette i contenitori distribuiti con Docker Compose:


```bash
user@ubuntu:~/compose$ docker ps
CONTAINER ID        IMAGE               COMMAND                CREATED             STATUS              PORTS                     NAMES
caf185d221b7        adtd/web:0.1        "apache2-foreground"   2 minutes ago       Up About a minute   10.0.0.4:80->80/tcp       swarm-agent-3B7093B8-0/compose_web_1
040efc0ea937        adtd/rest:0.1       "catalina.sh run"      3 minutes ago       Up 2 minutes        10.0.0.4:8080->8080/tcp   swarm-agent-3B7093B8-0/compose_rest_1
```

## Passaggi successivi

[Altre informazioni su Docker Swarm](https://docs.docker.com/swarm/)

<!---HONumber=AcomDC_0511_2016-->