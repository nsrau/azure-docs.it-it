---
title: Esercitazione per il servizio contenitore di Azure - Aggiornare un'applicazione | Microsoft Docs
description: Esercitazione per il servizio contenitore di Azure - Aggiornare un'applicazione
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenitori, Micro-Service, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 72cdbfe2fe65e5152ca748cbb3989e3ef980ee50
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---

# <a name="update-an-application-in-kubernetes"></a>Aggiornare un'applicazione in Kubernetes

Dopo aver distribuito un'applicazione in Kubernetes, è possibile aggiornarla specificando una nuova immagine del contenitore o una nuova versione dell'immagine. Quando si aggiorna un'applicazione, viene eseguito lo staging dell'implementazione dell'aggiornamento, in modo che solo una parte della distribuzione sia aggiornata contemporaneamente. Questo aggiornamento in staging consente all'applicazione di continuare la sua esecuzione durante l'aggiornamento e fornisce un meccanismo di rollback in caso si verifichi un errore nella distribuzione. 

In questa esercitazione, parte sei di sette, viene aggiornata l'app Azure Vote di esempio. Le attività da completare comprendono:

> [!div class="checklist"]
> * Aggiornamento del codice dell'applicazione front-end
> * Creazione di un'immagine del contenitore aggiornata
> * Push dell'immagine del contenitore in Registro contenitori di Azure
> * Distribuzione di un'immagine del contenitore aggiornata

Nelle esercitazioni successive, Operations Management Suite verrà configurato per monitorare il cluster Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi in Registro contenitori di Azure, ed è stato creato un cluster Kubernetes. L'applicazione è stata quindi eseguita nel cluster Kubernetes. 

Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Esercitazione 1 - Creare immagini del contenitore](./container-service-tutorial-kubernetes-prepare-app.md). 

## <a name="update-application"></a>Aggiornare l'applicazione

Per completare i passaggi di questa esercitazione, è necessario avere clonato una copia dell'applicazione Azure Vote. Se necessario, creare la copia clonata con il comando seguente:

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Aprire il file `config_file.cfg` con qualsiasi editor di testo o codice. È possibile trovare questo file nella directory seguente del repository clonato.

```bash
 /azure-voting-app-redis/azure-vote/azure-vote/config_file.cfg
```

Cambiare i valori per `VOTE1VALUE` e `VOTE2VALUE`, quindi salvare il file.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Blue'
VOTE2VALUE = 'Purple'
SHOWHOST = 'false'
```

Usare [docker-compose](https://docs.docker.com/compose/) per ricreare l'immagine front-end ed eseguire l'applicazione aggiornata.

```bash
docker-compose -f ./azure-voting-app-redis/docker-compose.yml up --build -d
```

## <a name="test-application-locally"></a>Testare l'applicazione in locale

Passare a `http://localhost:8080` per vedere l'applicazione aggiornata.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Applicare tag ed eseguire il push delle immagini

Applicare all'immagine *azure voto-anteriore* il tag loginServer del registro contenitori.

Se si usa il Registro contenitori di Azure, ottenere il nome di accesso del server con il comando [az acr list](/cli/azure/acr#list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Usare [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) per assegnare il tag all'immagine. Sostituire `<acrLoginServer>` con il nome di accesso del server del Registro contenitori di Azure o un nome host di un registro pubblico.

```bash
docker tag azure-vote-front <acrLoginServer>/azure-vote-front:redis-v2
```

Usare [docker push](https://docs.docker.com/engine/reference/commandline/push/) per caricare l'immagine nel registro. Sostituire `<acrLoginServer>` con il nome di accesso del server del Registro contenitori di Azure o un nome host di un registro pubblico.

```bash
docker push <acrLoginServer>/azure-vote-front:redis-v2
```

## <a name="deploy-update-application"></a>Distribuire l'aggiornamento dell'applicazione

Per garantire il tempo di attività massimo, è necessario eseguire più istanze del pod dell'applicazione. Verificare questa configurazione con il comando [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```bash
kubectl get pod
```

Output:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-217588096-5w632    1/1       Running   0          10m
azure-vote-front-233282510-b5pkz   1/1       Running   0          10m
azure-vote-front-233282510-dhrtr   1/1       Running   0          10m
azure-vote-front-233282510-pqbfk   1/1       Running   0          10m
```

Se non si dispone di più pod che eseguono l'immagine azure-vote-front, scalare la distribuzione *azure-vote-front*.


```azurecli-interactive
kubectl scale --replicas=3 deployment/azure-vote-front
```

Per aggiornare l'applicazione, usare il comando [kubectl set](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#set). Aggiornare `<acrLoginServer>` con il server di accesso o il nome host del registro contenitori.

```azurecli-interactive
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:redis-v2
```

Per monitorare la distribuzione, utilizzare il comando [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get). Quando l'applicazione aggiornata viene distribuita, le unità vengono terminate e ricreate con la nuova immagine del contenitore.

```azurecli-interactive
kubectl get pod
```

Output:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2978095810-gq9g0   1/1       Running   0          5m
azure-vote-front-1297194256-tpjlg   1/1       Running   0         1m
azure-vote-front-1297194256-tptnx   1/1       Running   0         5m
azure-vote-front-1297194256-zktw9   1/1       Terminating   0         1m
```

## <a name="test-updated-application"></a>Testare l'applicazione aggiornata

Ottenere l'indirizzo IP esterno del servizio *azure-vote-front*.

```azurecli-interactive
kubectl get service azure-vote-front
```

Passare all'indirizzo IP per vedere l'applicazione aggiornata.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata aggiornata un'applicazione e l'aggiornamento è stato distribuito in un cluster Kubernetes. Sono state completate le attività seguenti:

> [!div class="checklist"]
> * Aggiornamento del codice dell'applicazione front-end
> * Creazione di un'immagine del contenitore aggiornata
> * Push dell'immagine del contenitore in Registro contenitori di Azure
> * Distribuzione dell'applicazione aggiornata

Passare alla prossima esercitazione per apprendere come monitorare Kubernetes con Operations Management Suite.

> [!div class="nextstepaction"]
> [Monitorare Kubernetes con OMS](./container-service-tutorial-kubernetes-monitor.md)
