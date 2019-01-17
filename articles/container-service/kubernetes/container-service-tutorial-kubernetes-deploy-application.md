---
title: (DEPRECATO) Esercitazione sul servizio contenitore di Azure - Distribuire un'applicazione
description: Esercitazione sul servizio Azure Container - Distribuire un'applicazione
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/26/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: dafbb8d1221d5e9c6194611ad338b3714a089cea
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998788"
---
# <a name="deprecated-run-applications-in-kubernetes"></a>(DEPRECATO) Eseguire applicazioni in Kubernetes

> [!TIP]
> Per la versione aggiornata di questa esercitazione che usa il servizio Azure Kubernetes, vedere [Esercitazione: Eseguire le applicazioni nel servizio Azure Kubernetes ](../../aks/tutorial-kubernetes-deploy-application.md).

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

In questa esercitazione, parte quattro di sette, verrà distribuita un'applicazione di esempio in un cluster Kubernetes. I passaggi completati comprendono:

> [!div class="checklist"]
> * Scaricare i file manifesto Kubernetes
> * Eseguire un'applicazione in Kubernetes
> * Test dell'applicazione

Nelle esercitazioni successive l'applicazione viene ridimensionata e aggiornata e Log Analytics viene configurato per monitorare il cluster Kubernetes.

Questa esercitazione presuppone una conoscenza di base dei concetti relativi a Kubernetes. Per informazioni dettagliate su Kubernetes, vedere la [documentazione di Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi nel Registro contenitori di Azure, ed è stato creato un cluster Kubernetes. 

Per completare questa esercitazione, è necessario il file manifesto Kubernetes `azure-vote-all-in-one-redis.yml` creato in precedenza. Questo file è stato scaricato con il codice sorgente dell'applicazione in un'esercitazione precedente. Verificare che sia stato clonato il repository e che si siano state cambiate le directory nel repository clonato.

Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Tutorial 1 – Create container images](./container-service-tutorial-kubernetes-prepare-app.md) (Esercitazione 1: Creare immagini del contenitore). 

## <a name="update-manifest-file"></a>Aggiornare il file manifesto

In questa esercitazione, il Registro contenitori di Azure è stato usato per archiviare un'immagine del contenitore. Prima di eseguire l'applicazione, è necessario aggiornare il nome del server di accesso del Registro contenitori di Azure nel file manifesto Kubernetes.

Ottenere il nome del server di accesso del Registro contenitori di Azure con il comando [az acr list](/cli/azure/acr#az-acr-list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Il file manifesto è stato creato in precedenza con un nome di server di accesso `microsoft`. Aprire il file con qualsiasi editor di testo. In questo esempio il file viene aperto con `vi`.

```bash
vi azure-vote-all-in-one-redis.yml
```

Sostituire `microsoft` con il nome del server di accesso del Registro contenitori di Azure. Questo valore è presente nella riga **47** del file manifesto.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Salvare e chiudere il file.

## <a name="deploy-application"></a>Distribuire un'applicazione

Usare il comando [kubectl create](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create) per eseguire l'applicazione. Questo comando analizza il file manifesto e creare gli oggetti Kubernetes definiti.

```azurecli-interactive
kubectl create -f azure-vote-all-in-one-redis.yml
```

Output:

```bash
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testare l'applicazione

Viene creato un [servizio di Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) che espone l'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti. 

Per monitorare lo stato, usare il comando [kubectl get service](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get) con l'argomento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

**EXTERNAL-IP** per il servizio `azure-vote-front` viene visualizzato inizialmente come `pending`. Dopo che l'indirizzo EXTERNAL-IP passa da `pending` a `IP address`, usare `CTRL-C` per arrestare il processo kubectl watch.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Per vedere l'applicazione, passare all'indirizzo IP esterno.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata distribuita un'applicazione Azure Vote in un cluster Kubernetes del servizio Azure Container. Le attività completate comprendono:  

> [!div class="checklist"]
> * Scaricare i file manifesto Kubernetes
> * Eseguire l'applicazione in Kubernetes
> * Testare l'applicazione

Passare all'esercitazione successiva per informazioni sulla scalabilità sia di un'applicazione Kubernetes sia dell'infrastruttura Kubernetes sottostante. 

> [!div class="nextstepaction"]
> [Scalare l'applicazione e l'infrastruttura Kubernetes](./container-service-tutorial-kubernetes-scale.md)