---
title: 'Esercitazione su Kubernetes in Azure: distribuire un''applicazione'
description: Esercitazione sul servizio contenitore di Azure - Distribuire un'applicazione
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: tutorial
ms.date: 10/24/2017
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 4468424a96b4949161218d495dd21f24285430fd
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="run-applications-in-azure-container-service-aks"></a>Eseguire applicazioni nel servizio contenitore di Azure

In questa esercitazione, parte 4 di 8, viene distribuita un'applicazione di esempio in un cluster Kubernetes. I passaggi completati comprendono:

> [!div class="checklist"]
> * Scaricare i file manifesto Kubernetes
> * Eseguire un'applicazione in Kubernetes
> * Test dell'applicazione

Nelle esercitazioni successive, l'applicazione viene aggiornata e ne vengono aumentate le istanze e Operations Management Suite viene configurato per monitorare il cluster Kubernetes.

In questa esercitazione si presuppone una conoscenza di base dei concetti Kubernetes, per informazioni dettagliate su Kubernetes, vedere il [Kubernetes documentazione][kubernetes-documentation].

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi nel Registro contenitori di Azure, ed è stato creato un cluster Kubernetes. 

Per completare questa esercitazione, è necessario il file manifesto Kubernetes `azure-vote-all-in-one-redis.yaml` creato in precedenza. Questo file è stato scaricato con il codice sorgente dell'applicazione in un'esercitazione precedente. Verificare che sia stato clonato il repository e che si siano state cambiate le directory nel repository clonato.

Se si è già questi passaggi e si desidera seguire la procedura, tornare alla [esercitazione 1: creare le immagini contenitore][aks-tutorial-prepare-app].

## <a name="update-manifest-file"></a>Aggiornare il file manifesto

In questa esercitazione, il Registro contenitori di Azure è stato usato per archiviare un'immagine del contenitore. Prima di eseguire l'applicazione, è necessario aggiornare il nome del server di accesso del Registro contenitori di Azure nel file manifesto Kubernetes.

Ottenere il nome del server di accesso di record con il [elenco acr az] [ az-acr-list] comando.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Il file manifesto è stato creato in precedenza con un nome di server di accesso `microsoft`. Aprire il file con qualsiasi editor di testo. In questo esempio il file viene aperto con `vi`.

```console
vi azure-vote-all-in-one-redis.yaml
```

Sostituire `microsoft` con il nome del server di accesso del Registro contenitori di Azure. Questo valore è presente nella riga **47** del file manifesto.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

Salvare e chiudere il file.

## <a name="deploy-application"></a>Distribuire un'applicazione

Utilizzare il [kubectl creare] [ kubectl-create] comando per eseguire l'applicazione. Questo comando consente di analizzare il file manifesto e crea oggetti Kubernetes definiti.

```azurecli
kubectl create -f azure-vote-all-in-one-redis.yaml
```

Output:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testare l'applicazione

Oggetto [Kubernetes servizio] [ kubernetes-service] creato che espone l'applicazione a internet. Il processo potrebbe richiedere alcuni minuti. 

Per monitorare lo stato di avanzamento, usare il [kubectl ottenere servizio] [ kubectl-get] comando con il `--watch` argomento.

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
> [Scala Kubernetes applicazione e dell'infrastruttura][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
