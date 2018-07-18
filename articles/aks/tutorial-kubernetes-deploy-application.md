---
title: "Esercitazione su Kubernetes in Azure: distribuire un'applicazione"
description: Esercitazione sul servizio contenitore di Azure - Distribuire un'applicazione
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 02/22/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e0e349361afaac9aec816d7f5d158322d6f4e691
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/29/2018
ms.locfileid: "37101047"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Esercitazione: eseguire applicazioni in Azure Kubernetes Service (AKS)

In questa esercitazione, parte quattro di sette, verrà distribuita un'applicazione di esempio in un cluster Kubernetes. I passaggi completati comprendono:

> [!div class="checklist"]
> * Scaricare i file manifesto Kubernetes
> * Eseguire un'applicazione in Kubernetes
> * Test dell'applicazione

Nelle esercitazioni successive, questa applicazione è scalata e aggiornata.

Questa esercitazione presuppone una conoscenza di base dei concetti relativi a Kubernetes. Per informazioni dettagliate su Kubernetes, vedere la [documentazione di Kubernetes][kubernetes-documentation].

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi nel Registro contenitori di Azure, ed è stato creato un cluster Kubernetes.

Per completare questa esercitazione, è necessario il file manifesto Kubernetes `azure-vote-all-in-one-redis.yaml` creato in precedenza. Questo file è stato scaricato con il codice sorgente dell'applicazione in un'esercitazione precedente. Verificare che sia stato clonato il repository e che si siano state cambiate le directory nel repository clonato.

Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Tutorial 1 – Create container images][aks-tutorial-prepare-app] (Esercitazione 1: Creare immagini del contenitore).

## <a name="update-manifest-file"></a>Aggiornare il file manifesto

In questa esercitazione, il Registro contenitori di Azure è stato usato per archiviare un'immagine del contenitore. Prima di eseguire l'applicazione, è necessario aggiornare il nome del server di accesso del Registro contenitori di Azure nel file manifesto Kubernetes.

Ottenere il nome del server di accesso del Registro contenitori di Azure con il comando [az acr list][az-acr-list].

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Il file manifesto è stato creato in precedenza con un nome di server di accesso `microsoft`. Aprire il file con qualsiasi editor di testo. In questo esempio il file viene aperto con `nano`.

```console
nano azure-vote-all-in-one-redis.yaml
```

Sostituire `microsoft` con il nome del server di accesso del Registro contenitori di Azure. Questo valore è presente nella riga **47** del file manifesto.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Il codice precedente diventa quindi

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Salvare e chiudere il file.

## <a name="deploy-application"></a>Distribuire un'applicazione

Usare il comando [kubectl apply][kubectl-apply] per eseguire l'applicazione. Questo comando analizza il file manifesto e crea gli oggetti Kubernetes definiti.

```azurecli
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

Output:

```
deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-application"></a>Testare l'applicazione

Viene creato un [servizio di Kubernetes][kubernetes-service] che espone l'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti.

Per monitorare lo stato, usare il comando [kubectl get service][kubectl-get] con l'argomento `--watch`.

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

Se l'applicazione non è stata caricata, potrebbe essere presente un problema di autorizzazione con il registro di immagini.

Seguire questa procedura per [consentire l'accesso tramite un segreto Kubernetes](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione l'applicazione di voto di Azure è stata distribuita in un cluster Kubernetes nel servizio contenitore di Azure. Le attività completate comprendono:

> [!div class="checklist"]
> * Scaricare i file manifesto Kubernetes
> * Eseguire l'applicazione in Kubernetes
> * Testare l'applicazione

Passare all'esercitazione successiva per informazioni sulla scalabilità sia di un'applicazione Kubernetes sia dell'infrastruttura Kubernetes sottostante.

> [!div class="nextstepaction"]
> [Scalare l'applicazione e l'infrastruttura Kubernetes][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubernetes-documentation]: https://kubernetes.io/docs/home/
[kubernetes-service]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr#list
