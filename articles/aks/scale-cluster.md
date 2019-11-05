---
title: Ridimensionare un cluster del servizio Azure Kubernetes
description: Scopri come ridimensionare il numero di nodi in un cluster del servizio Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: iainfou
ms.openlocfilehash: 719f45aeeb5c7aa7e9b5e597ed461808c9d2b005
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73472592"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Ridimensionare il numero di nodi in un cluster del servizio Azure Kubernetes (AKS)

Se la risorsa richiede la modifica delle applicazioni, è possibile ridimensionare manualmente un cluster del servizio Azure Kubernetes per eseguire un numero diverso di nodi. Quando si esegue la riduzione, i nodi vengono accuratamente sottoposte a [cordoning e svuotati][kubernetes-drain] per ridurre al minimo l'interruzione delle applicazioni in esecuzione. Quando si esegue la scalabilità verticale, AKS attende che i nodi siano contrassegnati `Ready` dal cluster Kubernetes prima che i pod siano pianificati.

## <a name="scale-the-cluster-nodes"></a>Ridimensionare i nodi del cluster

Per prima cosa, ottenere il *nome* del pool di nodi usando il comando [AZ AKS Show][az-aks-show] . Nell'esempio seguente viene ottenuto il nome del pool di nodi per il cluster denominato *myAKSCluster* nel gruppo di risorse *myResourceGroup* :

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

Usare il comando [AZ AKS scale][az-aks-scale] per ridimensionare i nodi del cluster. L'esempio seguente ridimensiona un cluster denominato *myAKSCluster* passando a un singolo nodo. Specificare il proprio *--nodepool-name* dal comando precedente, ad esempio *nodepool1*:

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

In questo articolo è stato ridimensionato manualmente un cluster AKS per aumentare o diminuire il numero di nodi. È anche possibile usare il servizio di [scalabilità][cluster-autoscaler] automatica del cluster per ridimensionare automaticamente il cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
