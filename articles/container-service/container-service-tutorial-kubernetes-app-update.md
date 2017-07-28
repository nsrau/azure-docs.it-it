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
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2017
ms.author: nepeters
ms.translationtype: Human Translation
ms.sourcegitcommit: bb794ba3b78881c967f0bb8687b1f70e5dd69c71
ms.openlocfilehash: ce4a88a7958116890ec17eb2405ba809487b9c0f
ms.contentlocale: it-it
ms.lasthandoff: 07/06/2017

---

# <a name="update-an-application-in-kubernetes"></a>Aggiornare un'applicazione in Kubernetes

Dopo aver distribuito un'applicazione in Kubernetes, è possibile aggiornarla specificando una nuova immagine del contenitore o una nuova versione dell'immagine. Quando si aggiorna un'applicazione, viene eseguito lo staging dell'implementazione dell'aggiornamento, in modo che solo una parte della distribuzione sia aggiornata contemporaneamente. 

Questo aggiornamento in staging consente all'applicazione di continuare la sua esecuzione durante l'aggiornamento e fornisce un meccanismo di rollback in caso si verifichi un errore nella distribuzione. 

In questa esercitazione viene aggiornata l'app Azure di esempio Vote. Le attività da completare comprendono:

> [!div class="checklist"]
> * Aggiornamento del codice dell'applicazione front-end.
> * Creazione di un'immagine del contenitore aggiornata.
> * Push dell'immagine del contenitore nel Registro contenitori di Azure.
> * Distribuzione di un'applicazione aggiornata.

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione come immagini del contenitore, le immagini sono state caricate nel Registro contenitori di Azure ed è stato creato un cluster Kubernetes. È stata quindi eseguita l'applicazione nel cluster Kubernetes. 

Se non sono stati eseguiti questi passaggi e si desidera provarli ora, tornare a [Esercitazione 1: Creare le immagini del contenitore](./container-service-tutorial-kubernetes-prepare-app.md). 

Come minimo, questa esercitazione richiede un cluster Kubernetes con un'applicazione in esecuzione.

## <a name="update-application"></a>Aggiornare l'applicazione

Per completare i passaggi di questa esercitazione, è necessario avere clonato una copia dell'applicazione Azure Vote. Se necessario, creare la copia clonata con il comando seguente:

```bash
git clone https://github.com/Azure-Samples/azure-voting-app.git
```

Aprire il file `config_file.cfg` con qualsiasi editor di testo o codice. È possibile trovare questo file nella directory seguente del repository clonato.

```bash
 /azure-voting-app/azure-vote/azure-vote/config_file.cfg
```

Cambiare i valori per `VOTE1VALUE` e `VOTE2VALUE`, quindi salvare il file.

```bash
# UI Configurations
TITLE = 'Azure Voting App'
VOTE1VALUE = 'Half Full'
VOTE2VALUE = 'Half Empty'
SHOWHOST = 'false'
```

Usare `docker build` per ricreare l'immagine del front-end.

```bash
docker build --no-cache ./azure-voting-app/azure-vote -t azure-vote-front:v2
```

## <a name="test-application"></a>Testare l'applicazione

Creare una rete Docker. Questa rete viene utilizzata per la comunicazione tra i contenitori.  

```bash
docker network create azure-vote
```

Eseguire un'istanza dell'immagine del contenitore back-end usando il comando `docker run`.

```bash
docker run -p 3306:3306 --name azure-vote-back -d --network azure-vote -e MYSQL_ROOT_PASSWORD=Password12 -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote azure-vote-back 
```

Eseguire un'istanza dell'immagine del contenitore front-end.

```bash
docker run -d -p 8080:80 --name azure-vote-front --network=azure-vote -e MYSQL_USER=dbuser -e MYSQL_PASSWORD=Password12 -e MYSQL_DATABASE=azurevote -e MYSQL_HOST=azure-vote-back azure-vote-front:v2
```

Passare a `http://localhost:8080` per vedere l'applicazione aggiornata. L'applicazione impiega alcuni secondi per l'inizializzazione. Se si verifica un errore, riprovare.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/vote-app-updated.png)

## <a name="tag-and-push-images"></a>Applicare tag ed eseguire il push delle immagini

Applicare all'immagine *azure voto-anteriore* il tag loginServer del registro contenitori.

Se si usa il Registro contenitori di Azure, ottenere il nome di accesso del server con il comando [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Usare [docker tag](https://docs.docker.com/engine/reference/commandline/tag/) per assegnare il tag all'immagine, assicurandosi di aggiornare il comando con il server di accesso del Registro contenitori di Azure o un nome host di un registro pubblico.

```bash
docker tag azure-vote-front:v2 <acrLoginServer>/azure-vote-front:v2
```

Eseguire il push dell'immagine nel registro. Sostituire `<acrLoginServer>` con il nome di accesso del server del Registro contenitori di Azure o un nome host di un registro pubblico.

```bash
docker push <acrLoginServer>/azure-vote-front:v2
```

## <a name="deploy-update-to-kubernetes"></a>Distribuire l'aggiornamento a Kubernetes

### <a name="verify-multiple-pod-replicas"></a>Verificare più repliche POD

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


```bash
kubectl scale --replicas=3 deployment/azure-vote-front
```

### <a name="update-application"></a>Aggiornare l'applicazione

Per aggiornare l'applicazione, eseguire il seguente comando. Aggiornare `<acrLoginServer>` con il server di accesso o il nome host del registro contenitori.

```bash
kubectl set image deployment azure-vote-front azure-vote-front=<acrLoginServer>/azure-vote-front:v2
```

Per monitorare la distribuzione, utilizzare il comando [kubectl get pod](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get). Quando l'applicazione aggiornata viene distribuita, le unità vengono terminate e ricreate con la nuova immagine del contenitore.

```bash
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

```bash
kubectl get service
```

Passare all'indirizzo IP per vedere l'applicazione aggiornata.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/vote-app-updated-external.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione abbiamo aggiornato un'applicazione e implementato questo aggiornamento in un cluster Kubernetes. Abbiamo completato le attività seguenti:  

> [!div class="checklist"]
> * Aggiornamento del codice dell'applicazione front-end.
> * Creazione di un'immagine del contenitore aggiornata.
> * Push dell'immagini del contenitore nel Registro contenitori di Azure.
> * Distribuzione dell'applicazione aggiornata.

Passare alla prossima esercitazione per apprendere come monitorare Kubernetes con Operations Management Suite.

> [!div class="nextstepaction"]
> [Monitorare Kubernetes con OMS](./container-service-tutorial-kubernetes-monitor.md)
