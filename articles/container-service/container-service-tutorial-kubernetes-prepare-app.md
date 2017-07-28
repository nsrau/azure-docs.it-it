---
title: Esercitazione sul servizio contenitore di Azure - Preparare un'app | Microsoft Docs
description: Esercitazione sul servizio contenitore di Azure - App di preparazione
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenitori, Micro-Service, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: azurecli
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/26/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 49d727de69c700af0ae6402ad54b5880010ebb3b
ms.contentlocale: it-it
ms.lasthandoff: 06/28/2017

---

# <a name="create-container-images-to-be-used-with-azure-container-service"></a>Creare immagini del contenitore da usare con il servizio contenitore di Azure

In questa esercitazione viene preparata un'applicazione per Kubernetes. I passaggi completati comprendono:  

> [!div class="checklist"]
> * Clonazione dell'origine applicazione da GitHub  
> * Creazione di immagini del contenitore dall'origine applicazione
> * Test delle immagini in un ambiente Docker locale

Nelle esercitazioni successive, le immagini del contenitore vengono caricate in un Registro contenitori di Azure e quindi eseguite in un cluster Kubernetes ospitato in Azure.

## <a name="before-you-begin"></a>Prima di iniziare

Questa esercitazione presuppone una conoscenza di base dei concetti principali di Docker, come contenitori, immagini dei contenitore e comandi essenziali. Se necessario, vedere [Introduzione a Docker]( https://docs.docker.com/get-started/) per una panoramica sui concetti fondamentali relativi al contenitore. 

Per completare questa esercitazione è necessario un ambiente di sviluppo Docker. Docker offre pacchetti che consentono di configurare facilmente Docker in qualsiasi sistema [Mac](https://docs.docker.com/docker-for-mac/), [Windows](https://docs.docker.com/docker-for-windows/) o [Linux](https://docs.docker.com/engine/installation/#supported-platforms).

## <a name="get-application-code"></a>Ottenere il codice dell'applicazione

L'applicazione di esempio utilizzata in questa esercitazione è un'app di voto base. L'applicazione è costituita da un componente Web front-end e un database back-end. 

Usare git per scaricare una copia dell'applicazione per l'ambiente di sviluppo.

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

All'interno della directory dell'applicazione sono disponibili i file manifesto Dockerfile e Kubernetes creati in precedenza. Questi file vengono usati per creare le attività per l'esercitazione. 

## <a name="create-container-images"></a>Creare immagini del contenitore

Per creare un'immagine del contenitore per l'applicazione front-end, usare il comando [docker build](https://docs.docker.com/engine/reference/commandline/build/).

```bash
docker build ./azure-voting-app/azure-vote -t azure-vote-front
```

Ripetere il comando, questa volta per l'immagine del contenitore di back-end.

```bash
docker build ./azure-voting-app/azure-vote-mysql -t azure-vote-back
```

Al termine usare il comando `docker images` per vedere le immagini create. 

```bash
docker images
```

Output:

```bash
REPOSITORY                   TAG                 IMAGE ID            CREATED              SIZE
azure-vote-front             latest              c13c4f50ede1        39 seconds ago       716 MB
azure-vote-back              latest              33fe5afc1885        About a minute ago   407 MB
mysql                        latest              e799c7f9ae9c        4 weeks ago          407 MB
tiangolo/uwsgi-nginx-flask   flask               788ca94b2313        8 months ago         694 MB
```

## <a name="test-application"></a>Testare l'applicazione

Ora che sono state create due immagini del contenitore, è possibile verificare queste immagini nell'ambiente di sviluppo locale. 

Come prima operazione, creare una rete Docker. Questa rete viene utilizzata per la comunicazione tra i contenitori.  

```bash
docker network create azure-vote
```

Eseguire un'istanza dell'immagine del contenitore back-end usando il comando `docker run`.

In questo esempio, il file di database mysql è archiviato all'interno del contenitore. Dopo che l'applicazione è stata spostata nei cluster Kubernete, un volume di dati esterno viene usato per archiviare il file di database. Inoltre, vengono usate variabili di ambiente per impostare le credenziali di MySQL.

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

Eseguire un'istanza dell'immagine del contenitore front-end.

Le variabili di ambiente vengono usate per configurare le informazioni di connessione di database.

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front
```

Al termine, eseguire `docker ps` per vedere i contenitori in esecuzione.  

```bash
docker ps
```

Output:

```bash
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                           NAMES
3aa02e8ae965        azure-vote-front     "/usr/bin/supervisord"   59 seconds ago      Up 57 seconds       443/tcp, 0.0.0.0:8080->80/tcp   flaskmysqlvote_azure-vote-front_1
5ae60b3ba181        azure-vote-backend   "docker-entrypoint..."   59 seconds ago      Up 58 seconds       0.0.0.0:3306->3306/tcp          azure-vote-back
```

Passare a `http://localhost:8080` per vedere l'applicazione in esecuzione. L'applicazione impiega alcuni secondi per l'inizializzazione. Se si è verificato un errore, riprovare.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/vote-app.png)

## <a name="clean-up-resources"></a>Pulire le risorse

Ora che le funzionalità dell'applicazione sono state verificate, i contenitori in esecuzione possono essere arrestati e rimossi. Non eliminare le immagini del contenitore. Queste immagini saranno caricate in un'istanza del Registro contenitori di Azure nella prossima esercitazione.

Arrestare ed eliminare il contenitore front-end con il comando [docker rm](https://docs.docker.com/engine/reference/commandline/rm/). 

```bash
docker rm -f azure-vote-front
```

Arrestare ed eliminare il contenitore back-end con il comando [docker rm](https://docs.docker.com/engine/reference/commandline/rm/). 

```bash
docker rm -f azure-vote-back
```

Eliminare la rete con il comando [docker network rm](https://docs.docker.com/engine/reference/commandline/network_rm/).

```bash
docker network rm azure-vote
```

Al termine si dispone di due immagini del contenitore che costituiscono l'applicazione Vote di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata testata un'applicazione e sono state create le immagini del contenitore per l'applicazione stessa. Sono stati completati i passaggi seguenti:

> [!div class="checklist"]
> * Clonazione dell'origine applicazione da GitHub  
> * Creazione di immagini del contenitore dall'origine applicazione
> * Test delle immagini in un ambiente Docker locale

Passare alla prossima esercitazione per apprendere informazioni sull'archiviazione delle immagini del contenitore in un Registro contenitori di Azure.

> [!div class="nextstepaction"]
> [Eseguire il push delle immagini nel Registro contenitori di Azure](./container-service-tutorial-kubernetes-prepare-acr.md)
