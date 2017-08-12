---
title: Esercitazione sul servizio contenitore di Azure - Distribuire un'applicazione | Microsoft Docs
description: Esercitazione sul servizio contenitore di Azure - Distribuire un'applicazione
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
ms.date: 07/25/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 81a6a3d5364642b2da75faf875d64d2f4a1939d4
ms.contentlocale: it-it
ms.lasthandoff: 07/25/2017

---

# <a name="run-applications-in-kubernetes"></a>Eseguire applicazioni in Kubernetes

In questa esercitazione, parte quattro di sette, verrà distribuita un'applicazione di esempio in un cluster Kubernetes. I passaggi completati comprendono:

> [!div class="checklist"]
> * Scaricare i file manifesto Kubernetes
> * Eseguire un'applicazione in Kubernetes
> * Test dell'applicazione

Nelle esercitazioni successive, l'applicazione viene aggiornata e ne vengono aumentate le istanze e Operations Management Suite viene configurato per monitorare il cluster Kubernetes.

Questa esercitazione presuppone una conoscenza di base dei concetti relativi a Kubernetes. Per informazioni dettagliate su Kubernetes, vedere la [documentazione di Kubernetes](https://kubernetes.io/docs/home/).

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi nel Registro contenitori di Azure, ed è stato creato un cluster Kubernetes. Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Tutorial 1 – Create container images](./container-service-tutorial-kubernetes-prepare-app.md) (Esercitazione 1: Creare immagini del contenitore). 

Il requisito minimo per questa esercitazione è un cluster Kubernetes.

## <a name="get-manifest-file"></a>Ottenere il file manifesto

Per questa esercitazione, gli [oggetti Kubernetes](https://kubernetes.io/docs/concepts/overview/working-with-objects/kubernetes-objects/) vengono distribuiti con un manifesto Kubernetes. Un manifesto Kubernetes è un file in formato YAML o JSON contenente le istruzioni sulla distribuzione e la configurazione degli oggetti.

Il file manifesto dell'applicazione per questa esercitazione è disponibile nell'archivio dell'applicazione Azure Vote, che è stato clonato in un'esercitazione precedente. Se questa operazione non è già stata eseguita, clonare l'archivio con il comando seguente: 

```bash
git clone https://github.com/Azure-Samples/azure-voting-app-redis.git
```

Il file manifesto si trova nella directory seguente dell'archivio clonato.

```bash
/azure-voting-app-redis/kubernetes-manifests/
```

## <a name="update-manifest-file"></a>Aggiornare il file manifesto

Se si usa Registro contenitori di Azure per archiviare le immagini del contenitore, è necessario aggiornare il manifesto con il nome del server di accesso di Registro contenitori di Azure.

Ottenere il nome del server di accesso del Registro contenitori di Azure con il comando [az acr list](/cli/azure/acr#list).

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Il manifesto di esempio è stato creato in precedenza con il nome di repository *microsoft*. Aprire il file con qualsiasi editor di testo e sostituire il valore *microsoft* con il nome del server di accesso dell'istanza di Registro contenitori di Azure.

```yaml
containers:
- name: azure-vote-front
  image: microsoft/azure-vote-front:redis-v1
```

## <a name="deploy-application"></a>Distribuire un'applicazione

Usare il comando [kubectl create](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#create) per eseguire l'applicazione. Questo comando analizza il file manifesto e creare gli oggetti Kubernetes definiti.

```azurecli-interactive
kubectl create -f ./azure-voting-app-redis/kubernetes-manifests/azure-vote-all-in-one-redis.yaml
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

Per monitorare lo stato, usare il comando [kubectl get service](https://review.docs.microsoft.com/en-us/azure/container-service/container-service-kubernetes-walkthrough?branch=pr-en-us-17681) con l'argomento `--watch`.

```azurecli-interactive
kubectl get service azure-vote-front --watch
```

**EXTERNAL-IP** per il servizio *azure-vote-front* inizialmente viene visualizzato come *pending*. Dopo che l'indirizzo EXTERNAL-IP passa da *pending* a un *indirizzo IP*, usare `CTRL-C` per arrestare il processo kubectl watch.

```bash
NAME               CLUSTER-IP    EXTERNAL-IP   PORT(S)        AGE
azure-vote-front   10.0.42.158   <pending>     80:31873/TCP   1m
azure-vote-front   10.0.42.158   52.179.23.131 80:31873/TCP   2m
```

Per vedere l'applicazione, passare all'indirizzo IP esterno.

![Immagine del cluster Kubernetes in Azure](media/container-service-kubernetes-tutorials/azure-vote.png)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata distribuita un'applicazione Azure Vote in un cluster Kubernetes del servizio contenitore di Azure. Le attività completate comprendono:  

> [!div class="checklist"]
> * Scaricare i file manifesto Kubernetes
> * Eseguire l'applicazione in Kubernetes
> * Testare l'applicazione

Passare all'esercitazione successiva per informazioni sulla scalabilità sia di un'applicazione Kubernetes sia dell'infrastruttura Kubernetes sottostante. 

> [!div class="nextstepaction"]
> [Scalare l'applicazione e l'infrastruttura Kubernetes](./container-service-tutorial-kubernetes-scale.md)
