---
title: Esercitazione per il servizio contenitore di Azure - Scalare un'applicazione | Microsoft Docs
description: Esercitazione per il servizio contenitore di Azure - Scalare un'applicazione
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: Docker, contenitori, microservizi, Kubernetes, Azure
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/14/2017
ms.author: danlep
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: d24c6777cc6922d5d0d9519e720962e1026b1096
ms.openlocfilehash: bb528c424bc71f0309439e741c30e16d0d13c7d7
ms.contentlocale: it-it
ms.lasthandoff: 09/14/2017

---

# <a name="scale-kubernetes-pods-and-kubernetes-infrastructure"></a>Scalare i pod e l'infrastruttura Kubernetes

Se sono state eseguite le esercitazioni, si ha un cluster Kubernetes in funzione nel servizio contenitore di Azure ed è stata distribuita l'app Azure Voting. 

In questa esercitazione, la quinta di sette, si aumenterà il numero di istanze dei pod nell'app e si proverà la scalabilità automatica dei pod. Si apprenderà anche come ridimensionare il numero di nodi agente delle VM di Azure per modificare la capacità del cluster per l'hosting dei carichi di lavoro. Le attività completate comprendono:

> [!div class="checklist"]
> * Scalabilità manuale di pod Kubernetes
> * Configurazione della scalabilità automatica di pod che eseguono il front-end dell'app
> * Scalare i nodi agente Azure in Kubernetes

Nelle esercitazioni successive l'applicazione Azure Vote viene aggiornata e Operations Management Suite viene configurato per monitorare il cluster Kubernetes.

## <a name="before-you-begin"></a>Prima di iniziare

Nelle esercitazioni precedenti è stato creato un pacchetto di un'applicazione in un'immagine del contenitore, caricata poi nel Registro contenitori di Azure, ed è stato creato un cluster Kubernetes. L'applicazione è stata quindi eseguita nel cluster Kubernetes. 

Se questi passaggi non sono stati ancora eseguiti e si vuole procedere, tornare a [Tutorial 1 – Create container images](./container-service-tutorial-kubernetes-prepare-app.md) (Esercitazione 1: Creare immagini del contenitore). 

## <a name="manually-scale-pods"></a>Scalare manualmente i pod

Fino a questo momento sono stati distribuiti il front-end di Azure Vote e l'istanza di Redis, ognuno con una replica singola. Per verificare, eseguire il comando [kubectl get](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get).

```azurecli-interactive
kubectl get pods
```

Output:

```bash
NAME                               READY     STATUS    RESTARTS   AGE
azure-vote-back-2549686872-4d2r5   1/1       Running   0          31m
azure-vote-front-848767080-tf34m   1/1       Running   0          31m
```

Modificare manualmente il numero di pod nella distribuzione `azure-vote-front` usando il comando [kubectl scale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#scale). In questo esempio il numero aumenta a 5.

```azurecli-interactive
kubectl scale --replicas=5 deployment/azure-vote-front
```

Eseguire [kubectl get pods](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#get) per verificare che Kubernetes stia creando i pod. Dopo circa un minuto i pod aggiuntivi sono in esecuzione:

```azurecli-interactive
kubectl get pods
```

Output:

```bash
NAME                                READY     STATUS    RESTARTS   AGE
azure-vote-back-2606967446-nmpcf    1/1       Running   0          15m
azure-vote-front-3309479140-2hfh0   1/1       Running   0          3m
azure-vote-front-3309479140-bzt05   1/1       Running   0          3m
azure-vote-front-3309479140-fvcvm   1/1       Running   0          3m
azure-vote-front-3309479140-hrbf2   1/1       Running   0          15m
azure-vote-front-3309479140-qphz8   1/1       Running   0          3m
```

## <a name="autoscale-pods"></a>Scalare automaticamente i pod

Kubernetes supporta la [scalabilità automatica orizzontale dei pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) per modificare il numero dei pod in una distribuzione a seconda dell'utilizzo della CPU o delle altre metriche selezionate. 

Per usare la scalabilità automatica, i pod devono avere richieste e limiti di CPU definiti. Nella distribuzione di `azure-vote-front` il contenitore front-end richiede un valore di 0,25 della CPU, con un limite pari a 0,5 della CPU. Le impostazioni sono simili:

```YAML
resources:
  requests:
     cpu: 250m
  limits:
     cpu: 500m
```

L'esempio seguente usa il comando [kubectl autoscale](https://kubernetes.io/docs/user-guide/kubectl/v1.6/#autoscale) per scalare automaticamente il numero di pod nella distribuzione `azure-vote-front`. In questo caso, se l'utilizzo della CPU supera il 50%, la scalabilità automatica aumenta il numero di pod fino al valore massimo di 10.


```azurecli-interactive
kubectl autoscale deployment azure-vote-front --cpu-percent=50 --min=3 --max=10
```

Per visualizzare lo stato della scalabilità automatica, eseguire il comando seguente:

```azurecli-interactive
kubectl get hpa
```

Output:

```bash
NAME               REFERENCE                     TARGETS    MINPODS   MAXPODS   REPLICAS   AGE
azure-vote-front   Deployment/azure-vote-front   0% / 50%   3         10        3          2m
```

Dopo pochi minuti con un carico minimo sull'app Azure Vote, il numero di repliche di pod si riduce automaticamente a 3.

## <a name="scale-the-agents"></a>Scalare gli agenti

Se è stato creato usando i comandi predefiniti nell'esercitazione precedente, il cluster Kubernetes dispone di tre nodi agente. Se si prevede un numero maggiore o minore di carichi di lavoro dei contenitori nel cluster, è possibile modificare manualmente il numero di agenti. Usare il comando [az acs scale](/cli/azure/acs#scale) e specificare il numero di agenti con il parametro `--new-agent-count`.

Nell'esempio seguente il numero di nodi agente viene aumentato a 4 nel cluster Kubernetes, denominato *myK8sCluster*. Il completamento del comando richiede alcuni minuti.

```azurecli-interactive
az acs scale --resource-group=myResourceGroup --name=myK8SCluster --new-agent-count 4
```

L'output del comando mostra il numero di nodi agente nel valore di `agentPoolProfiles:count`:

```azurecli
{
  "agentPoolProfiles": [
    {
      "count": 4,
      "dnsPrefix": "myK8SCluster-myK8SCluster-e44f25-k8s-agents",
      "fqdn": "",
      "name": "agentpools",
      "vmSize": "Standard_D2_v2"
    }
  ],
...

```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state usate diverse funzionalità di scalabilità nel cluster Kubernetes. Le attività descritte includevano:

> [!div class="checklist"]
> * Scalabilità manuale di pod Kubernetes
> * Configurazione della scalabilità automatica di pod che eseguono il front-end dell'app
> * Scalare i nodi agente Azure in Kubernetes

Passare all'esercitazione successiva per apprendere come aggiornare l'applicazione in Kubernetes.

> [!div class="nextstepaction"]
> [Aggiornare un'applicazione in Kubernetes](./container-service-tutorial-kubernetes-app-update.md)


