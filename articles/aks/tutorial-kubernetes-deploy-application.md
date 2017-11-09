---
title: Esercitazione su Kubernetes in Azure - Distribuire un'applicazione | Microsoft Docs
description: Esercitazione sul servizio contenitore di Azure - Distribuire un'applicazione
services: container-service
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: aks, azure-container-service
keywords: Docker, contenitori, Micro-Service, Kubernetes, DC/OS, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 73b943709fb3846058d4b1c09bc76b09460855ed
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2017
---
# <a name="run-applications-in-azure-container-service-aks"></a>Eseguire applicazioni nel servizio contenitore di Azure

In questa esercitazione, parte 4 di 8, viene distribuita un'applicazione di esempio in un cluster Kubernetes. I passaggi completati comprendono:

> [!div class="checklist"]
> * Scaricare i file manifesto Kubernetes
> * Eseguire un'applicazione in Kubernetes
> * Test dell'applicazione

Nelle esercitazioni successive, l'applicazione viene aggiornata e ne vengono aumentate le istanze e Operations Management Suite viene configurato per monitorare il cluster Kubernetes.

Questa esercitazione presuppone una conoscenza di base dei concetti relativi a Kubernetes. Per informazioni dettagliate su Kubernetes, vedere la [documentazione di Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi nel Registro contenitori di Azure, ed è stato creato un cluster Kubernetes. 

Per completare questa esercitazione, è necessario il file manifesto Kubernetes `azure-vote-all-in-one-redis.yml` creato in precedenza. Questo file è stato scaricato con il codice sorgente dell'applicazione in un'esercitazione precedente. Verificare che sia stato clonato il repository e che si siano state cambiate le directory nel repository clonato.

Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Tutorial 1 – Create container images](./tutorial-kubernetes-prepare-app.md) (Esercitazione 1: Creare immagini del contenitore). 

## <a name="update-manifest-file"></a>Aggiornare il file manifesto

In questa esercitazione, il Registro contenitori di Azure è stato usato per archiviare un'immagine del contenitore. Prima di eseguire l'applicazione, è necessario aggiornare il nome del server di accesso del Registro contenitori di Azure nel file manifesto Kubernetes.

Ottenere il nome del server di accesso del Registro contenitori di Azure con il comando [az acr list](/cli/azure/acr#list).

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Il file manifesto è stato creato in precedenza con un nome di server di accesso `microsoft`. Aprire il file con qualsiasi editor di testo. In questo esempio il file viene aperto con `vi`.

```console
vi azure-vote-all-in-one-redis.yml
```

Sostituire `microsoft` con il nome del server di accesso del Registro contenitori di Azure. Questo valore è presente nella riga **47** del file manifesto.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

Salvare e chiudere il file.

## <a name="deploy-application"></a>Distribuire un'applicazione

Usare il comando [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) per eseguire l'applicazione. Questo comando analizza il file manifesto e creare gli oggetti Kubernetes definiti.

```azurecli
kubectl create -f azure-vote-all-in-one-redis.yml
```

Output:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testare l'applicazione

Viene creato un [servizio di Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) che espone l'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti. 

Per monitorare lo stato, usare il comando [kubectl get service](https://kubernetes.io/docs/user-guide/kubectl/v1.7/#get) con l'argomento `--watch`.

```azurecli
kubectl get service azure-vote-front --watch
```

*EXTERNAL-IP* per il servizio *azure-vote-front* inizialmente viene visualizzato come *pending*.
  
```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Dopo il passaggio di *EXTERNAL-IP* da *pending* a un *indirizzo IP*, usare `CTRL-C` per arrestare il processo kubectl watch. 

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Per vedere l'applicazione, passare all'indirizzo IP esterno.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione l'applicazione di voto di Azure è stata distribuita in un cluster Kubernetes nel servizio contenitore di Azure. Le attività completate comprendono:  

> [!div class="checklist"]
> * Scaricare i file manifesto Kubernetes
> * Eseguire l'applicazione in Kubernetes
> * Testare l'applicazione

Passare all'esercitazione successiva per informazioni sulla scalabilità sia di un'applicazione Kubernetes sia dell'infrastruttura Kubernetes sottostante. 

> [!div class="nextstepaction"]
> [Scalare l'applicazione e l'infrastruttura Kubernetes](./tutorial-kubernetes-scale.md)
