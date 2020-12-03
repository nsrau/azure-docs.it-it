---
title: Aggiornare le immagini del nodo di Azure Kubernetes Service (AKS)
description: Informazioni su come aggiornare le immagini nei nodi del cluster AKS e nei pool di nodi.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 83d7d48922806334e2b49494fe0ef1d15e1a7a6a
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96531480"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Aggiornamento dell'immagine del nodo del servizio Kubernetes di Azure (AKS)

AKS supporta l'aggiornamento delle immagini in un nodo, in modo da essere aggiornati con gli aggiornamenti più recenti del sistema operativo e del runtime. AKS fornisce una nuova immagine alla settimana con gli aggiornamenti più recenti, quindi è vantaggioso aggiornare regolarmente le immagini del nodo per le funzionalità più recenti, incluse le patch di Linux o Windows. Questo articolo illustra come aggiornare le immagini del nodo del cluster AKS e come aggiornare le immagini del pool di nodi senza aggiornare la versione di Kubernetes.

Per altre informazioni sulle immagini più recenti fornite da AKS, vedere le [Note sulla versione di AKS](https://github.com/Azure/AKS/releases).

Per informazioni sull'aggiornamento della versione di Kubernetes per il cluster, vedere [aggiornare un cluster AKS][upgrade-cluster].

> [!NOTE]
> Il cluster AKS deve usare i set di scalabilità di macchine virtuali per i nodi.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Verificare se il pool di nodi è nell'immagine del nodo più recente

È possibile vedere qual è la versione più recente dell'immagine del nodo disponibile per il pool di nodi con il comando seguente: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

Nell'output è possibile vedere come nell' `latestNodeImageVersion` esempio seguente:

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

Quindi `nodepool1` , per l'immagine del nodo più recente disponibile è `AKSUbuntu-1604-2020.10.28` . È ora possibile confrontarlo con la versione corrente dell'immagine del nodo utilizzata dal pool di nodi eseguendo:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Di seguito è riportato un esempio di output:

```output
"AKSUbuntu-1604-2020.10.08"
```

Quindi, in questo esempio, è possibile eseguire l'aggiornamento dalla versione corrente dell' `AKSUbuntu-1604-2020.10.08` immagine alla versione più recente `AKSUbuntu-1604-2020.10.28` . 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Aggiornare tutti i nodi in tutti i pool di nodi

L'aggiornamento dell'immagine del nodo viene eseguito con `az aks upgrade` . Per aggiornare l'immagine del nodo, utilizzare il comando seguente:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Durante l'aggiornamento, controllare lo stato delle immagini del nodo con il `kubectl` comando seguente per ottenere le etichette e filtrare le informazioni sull'immagine del nodo corrente:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Al termine dell'aggiornamento, usare `az aks show` per ottenere i dettagli del pool di nodi aggiornati. L'immagine del nodo corrente viene visualizzata nella `nodeImageVersion` Proprietà.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Aggiornare un pool di nodi specifico

L'aggiornamento dell'immagine in un pool di nodi è simile all'aggiornamento dell'immagine in un cluster.

Per aggiornare l'immagine del sistema operativo del pool di nodi senza eseguire l'aggiornamento di un cluster Kubernetes, usare l' `--node-image-only` opzione nell'esempio seguente:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Durante l'aggiornamento, controllare lo stato delle immagini del nodo con il `kubectl` comando seguente per ottenere le etichette e filtrare le informazioni sull'immagine del nodo corrente:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Al termine dell'aggiornamento, usare `az aks nodepool show` per ottenere i dettagli del pool di nodi aggiornati. L'immagine del nodo corrente viene visualizzata nella `nodeImageVersion` Proprietà.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Aggiornare le immagini del nodo con picchi di nodo

Per velocizzare il processo di aggiornamento dell'immagine del nodo, è possibile aggiornare le immagini del nodo usando un valore di aumento del nodo personalizzabile. Per impostazione predefinita, AKS usa un nodo aggiuntivo per configurare gli aggiornamenti.

Se si vuole aumentare la velocità degli aggiornamenti, usare il `--max-surge` valore per configurare il numero di nodi da usare per gli aggiornamenti in modo che vengano completati più velocemente. Per altre informazioni sui compromessi delle diverse `--max-surge` Impostazioni, vedere Personalizzare l' [aggiornamento dell'aumento dei nodi][max-surge].

Il seguente comando imposta il valore di aumento massimo per l'esecuzione di un aggiornamento dell'immagine del nodo:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Durante l'aggiornamento, controllare lo stato delle immagini del nodo con il `kubectl` comando seguente per ottenere le etichette e filtrare le informazioni sull'immagine del nodo corrente:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Usare `az aks nodepool show` per ottenere i dettagli del pool di nodi aggiornati. L'immagine del nodo corrente viene visualizzata nella `nodeImageVersion` Proprietà.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Passaggi successivi

- Vedere le [Note sulla versione di AKS](https://github.com/Azure/AKS/releases) per informazioni sulle immagini del nodo più recenti.
- Informazioni su come aggiornare la versione di Kubernetes con l' [aggiornamento di un cluster AKS][upgrade-cluster].
- [Applicare automaticamente gli aggiornamenti del cluster e del pool di nodi con azioni di GitHub][github-schedule]
- Altre informazioni su più pool di nodi e su come aggiornare i pool di nodi con [creare e gestire pool][use-multiple-node-pools]di nodi multipli.

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
