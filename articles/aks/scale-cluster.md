---
title: Ridimensionare un cluster del servizio Azure Kubernetes
description: Scopri come ridimensionare il numero di nodi in un cluster del servizio Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: d5686a74ffe138af51d2319c839a3a5c5887f992
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902941"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Ridimensionare il numero di nodi in un cluster del servizio Azure Kubernetes (AKS)

Se la risorsa richiede la modifica delle applicazioni, è possibile ridimensionare manualmente un cluster del servizio Azure Kubernetes per eseguire un numero diverso di nodi. In caso di riduzione, i nodi verranno accuratamente [contrassegnati come non pianificabili e svuotati][kubernetes-drain] per ridurre al minimo le interruzioni nelle applicazioni in esecuzione. Quando si esegue la scalabilità verticale, AKS attende che i nodi siano contrassegnati `Ready` dal cluster Kubernetes prima che i pod siano pianificati.

## <a name="scale-the-cluster-nodes"></a>Ridimensionare i nodi del cluster

Per prima cosa, ottenere il *nome* del pool di nodi usando il comando [AZ AKS Show][az-aks-show] . Nell'esempio seguente viene ottenuto il nome del pool di nodi per il cluster denominato *myAKSCluster* nel gruppo di risorse *myResourceGroup* :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

L'output di esempio seguente illustra che il *nome* è *nodepool1*:

```output
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

Usare il comando [AZ AKS scale][az-aks-scale] per ridimensionare i nodi del cluster. L'esempio seguente ridimensiona un cluster denominato *myAKSCluster* passando a un singolo nodo. Specificare `--nodepool-name` il comando precedente, ad esempio *nodepool1*:

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


## <a name="scale-user-node-pools-to-0"></a>Ridimensionare i `User` pool di nodi a 0

A differenza dei `System` pool di nodi che richiedono sempre nodi in esecuzione, i `User` pool di nodi consentono di applicare la scalabilità a 0. Per ulteriori informazioni sulle differenze tra i pool di nodi del sistema e degli utenti, vedere [pool di nodi di sistema e utente](use-system-pools.md).

Per ridimensionare un pool di utenti a 0, è possibile usare il comando [AZ AKS nodepool scale][az-aks-nodepool-scale] in alternativa al `az aks scale` comando precedente e impostare 0 come numero di nodi.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

È anche possibile ridimensionare automaticamente `User` i pool di nodi a 0 nodi, impostando il `--min-count` parametro del [ridimensionatore](cluster-autoscaler.md) automatico del cluster su 0.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato ridimensionato manualmente un cluster AKS per aumentare o diminuire il numero di nodi. È anche possibile usare il servizio di [scalabilità][cluster-autoscaler] automatica del cluster per ridimensionare automaticamente il cluster.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale&preserve-view=true