---
title: Ridimensionare un cluster del servizio Azure Kubernetes
description: Scopri come ridimensionare il numero di nodi in un cluster del servizio Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfoulds
ms.openlocfilehash: de3f8613c93715aecf7e9e066a8ad1d82e4379e3
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475140"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Ridimensionare il numero di nodi in un cluster del servizio Azure Kubernetes (AKS)

Se la risorsa richiede la modifica delle applicazioni, è possibile ridimensionare manualmente un cluster del servizio Azure Kubernetes per eseguire un numero diverso di nodi. In caso di riduzione, i nodi verranno accuratamente [contrassegnati come non pianificabili e svuotati][kubernetes-drain] per ridurre al minimo le interruzioni nelle applicazioni in esecuzione. Quando si aumentano, AKS attende fino a quando i nodi sono contrassegnati `Ready` dal cluster Kubernetes prima i POD vengono pianificati su di essi.

## <a name="scale-the-cluster-nodes"></a>Ridimensionare i nodi del cluster

Innanzitutto, ottenere il *name* del nodo del pool usando la [show di az aks] [ az-aks-show] comando. L'esempio seguente ottiene il nome del pool di nodi del cluster denominato *myAKSCluster* nel *myResourceGroup* gruppo di risorse:

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

L'output di esempio seguente illustra che il *nome* è *nodepool1*:

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles

[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Usare la [scala di az aks] [ az-aks-scale] comando per ridimensionare i nodi del cluster. L'esempio seguente ridimensiona un cluster denominato *myAKSCluster* passando a un singolo nodo. Specificare il proprio *--nodepool-name* dal comando precedente, ad esempio *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

L'output di esempio seguente mostra che il cluster è stato ridimensionato a un nodo, come illustrato nella sezione *agentPoolProfiles*:

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo, si manualmente ridimensionati in un cluster servizio contenitore di AZURE per aumentare o diminuire il numero di nodi. È anche possibile usare la [ridimensionamento automatico del cluster] [ cluster-autoscaler] (attualmente in anteprima nel servizio contenitore di AZURE) per ridimensionare automaticamente il cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
