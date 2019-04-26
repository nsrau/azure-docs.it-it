---
title: Esercitazione su Kubernetes in Azure - Distribuire un'applicazione
description: In questa esercitazione sul servizio Azure Kubernetes si distribuisce un'applicazione multicontenitore nel cluster usando un'immagine archiviata in Registro Azure Container.
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: zarhoads
ms.custom: mvc
ms.openlocfilehash: dc3d4f0b2658c82a50f58d976a47607dead1abce
ms.sourcegitcommit: c884e2b3746d4d5f0c5c1090e51d2056456a1317
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60149530"
---
# <a name="tutorial-run-applications-in-azure-kubernetes-service-aks"></a>Esercitazione: Eseguire le applicazioni nel servizio Azure Kubernetes

Kubernetes fornisce una piattaforma distribuita per applicazioni in contenitori. Si compilano e si distribuiscono le applicazioni e i servizi in un cluster Kubernetes e si consente al cluster di gestire la disponibilità e la connettività. In questa esercitazione, parte quattro di sette, verrà distribuita un'applicazione di esempio in un cluster Kubernetes. Si apprenderà come:

> [!div class="checklist"]
> * Aggiornare un file manifesto Kubernetes
> * Eseguire un'applicazione in Kubernetes
> * Test dell'applicazione

Nelle altre esercitazioni questa applicazione è stata ridimensionata e aggiornata.

Questa guida introduttiva presuppone una comprensione di base dei concetti relativi a Kubernetes. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi in Registro Azure Container, ed è stato creato un cluster Kubernetes.

Per completare questa esercitazione, è necessario il file manifesto Kubernetes `azure-vote-all-in-one-redis.yaml` creato in precedenza. Questo file è stato scaricato con il codice sorgente dell'applicazione in un'esercitazione precedente. Verificare che il repository sia stato clonato e che le directory siano state impostate sul repository clonato. Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, iniziare con l'[Esercitazione 1: Creare immagini del contenitore][aks-tutorial-prepare-app].

Per questa esercitazione è necessario eseguire l'interfaccia della riga di comando di Azure versione 2.0.53 o successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

## <a name="update-the-manifest-file"></a>Aggiornare il file manifesto

In queste esercitazioni un'istanza di Registro Azure Container archivia l'immagine del contenitore per l'applicazione di esempio. Per distribuire l'applicazione, è necessario aggiornare il nome dell'immagine nel file manifesto di Kubernetes includendo il nome del server di accesso di Registro Azure Container.

Ottenere il nome del server di accesso di Registro Azure Container usando il comando [az acr list][az-acr-list] come di seguito:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Il file manifesto di esempio del repository Git clonato nella prima esercitazione usa il nome del server di accesso *microsoft*. Assicurarsi di essere nella directory clonata *azure-voting-app-redis* e quindi aprire il file manifesto in un editor di testo, ad esempio `vi`:

```console
vi azure-vote-all-in-one-redis.yaml
```

Sostituire *microsoft* con il nome del server di accesso di Registro Azure Container. Il nome dell'immagine è presente nella riga 51 del file manifesto. L'esempio seguente mostra il nome dell'immagine predefinito:

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:v1
```

Immettere il proprio nome del server di accesso di Registro Azure Container in modo che il file manifesto abbia un aspetto simile al seguente:

```yaml
containers:
- name: azure-vote-front
  image: <acrName>.azurecr.io/azure-vote-front:v1
```

Salvare e chiudere il file. In `vi` usare `:wq`.

## <a name="deploy-the-application"></a>Distribuire l'applicazione

Per distribuire l'applicazione, usare il comando [kubectl apply][kubectl-apply]. Questo comando analizza il file manifesto e crea gli oggetti Kubernetes definiti. Specificare il file manifesto di esempio, come illustrato nell'esempio seguente:

```console
kubectl apply -f azure-vote-all-in-one-redis.yaml
```

L'output di esempio seguente mostra che le risorse sono state create correttamente nel cluster del servizio Azure Kubernetes:

```
$ kubectl apply -f azure-vote-all-in-one-redis.yaml

deployment "azure-vote-back" created
service "azure-vote-back" created
deployment "azure-vote-front" created
service "azure-vote-front" created
```

## <a name="test-the-application"></a>Test dell'applicazione

Durante l'esecuzione dell'applicazione, un servizio Kubernetes espone il front-end dell'applicazione a Internet. Il processo potrebbe richiedere alcuni minuti.

Per monitorare lo stato, usare il comando [kubectl get service][kubectl-get] con l'argomento `--watch`.

```console
kubectl get service azure-vote-front --watch
```

*EXTERNAL-IP* per il servizio *azure-vote-front* viene inizialmente visualizzato come *pending*:

```
azure-vote-front   10.0.34.242   <pending>     80:30676/TCP   7s
```

Quando *EXTERNAL-IP* passa da *pending* a un effettivo indirizzo IP pubblico, usare `CTRL-C` per arrestare il processo di controllo `kubectl`. L'output di esempio seguente mostra un indirizzo IP pubblico valido assegnato al servizio:

```
azure-vote-front   10.0.34.242   52.179.23.131   80:30676/TCP   2m
```

Per vedere l'applicazione in azione, aprire un Web browser all'indirizzo IP esterno del servizio:

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

Se l'applicazione non è stata caricata, potrebbe essersi verificato un problema di autorizzazione con il registro immagini. Per visualizzare lo stato dei contenitori, usare il comando`kubectl get pods`. Se non è possibile eseguire il pull delle immagini del contenitore, vedere [Consentire l'accesso a Registro Container con un segreto di Kubernetes](https://docs.microsoft.com/azure/container-registry/container-registry-auth-aks#access-with-kubernetes-secret).

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione l'applicazione di voto di Azure di esempio è stata distribuita in un cluster Kubernetes nel servizio Azure Kubernetes. Si è appreso come:

> [!div class="checklist"]
> * Aggiornare un file manifesto Kubernetes
> * Eseguire un'applicazione in Kubernetes
> * Test dell'applicazione

Passare all'esercitazione successiva per informazioni su come ridimensionare un'applicazione Kubernetes e l'infrastruttura Kubernetes sottostante.

> [!div class="nextstepaction"]
> [Scalare l'applicazione e l'infrastruttura Kubernetes][aks-tutorial-scale]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[aks-tutorial-scale]: ./tutorial-kubernetes-scale.md
[az-acr-list]: /cli/azure/acr
[azure-cli-install]: /cli/azure/install-azure-cli
[kubernetes-concepts]: concepts-clusters-workloads.md
[kubernetes-service]: concepts-network.md#services
