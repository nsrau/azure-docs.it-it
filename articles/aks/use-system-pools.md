---
title: Usare i pool di nodi di sistema nel servizio Azure Kubernetes (AKS)Use system node pools in Azure Kubernetes Service (AKS)
description: Informazioni su come creare e gestire pool di nodi di sistema nel servizio Azure Kubernetes (AKS)Learn how to create and manage system node pools in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 04/06/2020
ms.openlocfilehash: b567d9e618877463e1e659f368d35fbb787a4ef2
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81259069"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Gestire i pool di nodi di sistema nel servizio Azure Kubernetes (AKS)Manage system node pools in Azure Kubernetes Service (AKS)

Nel servizio Azure Kubernetes (AKS), i nodi della stessa configurazione vengono raggruppati in pool di *nodi.* I pool di nodi contengono le macchine virtuali sottostanti che eseguono le applicazioni. I pool di nodi di sistema e i pool di nodi utente sono due diverse modalità di pool di nodi per i cluster AKS. I pool di nodi di sistema hanno lo scopo principale di ospitare pod di sistema critici come CoreDNS e tunnelfront. I pool di nodi utente servono allo scopo principale dell'hosting dei pod dell'applicazione. Tuttavia, i pod dell'applicazione possono essere pianificati nei pool di nodi di sistema se si desidera avere un solo pool nel cluster AKS. Ogni cluster AKS deve contenere almeno un pool di nodi di sistema con almeno un nodo. 

> [!Important]
> Se si esegue un singolo pool di nodi di sistema per il cluster AKS in un ambiente di produzione, è consigliabile usare almeno tre nodi per il pool di nodi.

## <a name="before-you-begin"></a>Prima di iniziare

* È necessaria l'interfaccia della riga di comando di Azure versione 2.3.1 o successiva installata e configurata. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="limitations"></a>Limitazioni

Le limitazioni seguenti si applicano quando si creano e si gestiscono cluster AKS che supportano pool di nodi di sistema.

* Vedere Quote, restrizioni sulle dimensioni delle [macchine virtuali e disponibilità dell'area nel servizio Azure Kubernetes (AKS).][quotas-skus-regions]
* Il cluster AKS deve essere compilato con set di scalabilità di macchine virtuali come tipo di macchina virtuale.
* Il nome di un pool di nodi può contenere solo caratteri alfanumerici minuscoli e deve iniziare con una lettera minuscola. Per i pool di nodi Linux, la lunghezza deve essere compresa tra 1 e 12 caratteri. Per i pool di nodi di Windows, la lunghezza deve essere compresa tra 1 e 6 caratteri.

## <a name="system-and-user-node-pools"></a>Pool di nodi di sistema e utente

I nodi del pool di nodi di sistema dispongono ciascuno dell'etichetta **kubernetes.azure.com/mode: system .** Ogni cluster AKS contiene almeno un pool di nodi di sistema. I pool di nodi di sistema presentano le restrizioni seguenti:System node pools have the following restrictions:

* I pool di sistema osType devono essere Linux.System pools osType must be Linux.
* I pool di nodi utente osType possono essere Linux o Windows.User node pools osType may be Linux or Windows.
* I pool di sistema devono contenere almeno un nodo e i pool di nodi utente possono contenere zero o più nodi.
* I pool di nodi di sistema richiedono uno SKU della macchina virtuale di almeno 2 vCPU e una memoria di 4 GB.
* I pool di nodi di sistema devono supportare almeno 30 pod, come descritto dalla formula del [valore minimo e massimo per i pod][maximum-pods].
* I pool di nodi spot richiedono pool di nodi utente.

È possibile eseguire le operazioni seguenti con i pool di nodi:You can do the following operations with node pools:

* Modificare un pool di nodi di sistema in modo che sia un pool di nodi utente, a condizione che si disponga di un altro pool di nodi di sistema per prendere il suo posto nel cluster AKS.
* Modificare un pool di nodi utente in pool di nodi di sistema.
* Eliminare i pool di nodi utente.
* È possibile eliminare i pool di nodi di sistema, purché si disponga di un altro pool di nodi di sistema per prendere il suo posto nel cluster AKS.
* Un cluster AKS può avere più pool di nodi di sistema e richiede almeno un pool di nodi di sistema.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Creare un nuovo cluster AKS con un pool di nodi di sistemaCreate a new AKS cluster with a system node pool

Quando si crea un nuovo cluster AKS, si crea automaticamente un pool di nodi di sistema con un singolo nodo. Il pool di nodi iniziale per impostazione predefinita è una modalità di tipo sistema. Quando si creano nuovi pool di nodi con az aks nodepool add, tali pool di nodi sono pool di nodi utente a meno che non si specifichi in modo esplicito il parametro mode.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area *eastus.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Usare il comando [az servizio Azure Kubernetes create][az-aks-create] per creare un cluster del servizio Azure Container. Nell'esempio seguente viene creato un cluster denominato *myAKSCluster* con un pool di sistema contenente un nodo. Per i carichi di lavoro di produzione, assicurarsi di utilizzare pool di nodi di sistema con almeno tre nodi. Il completamento dell'operazione può richiedere alcuni minuti.

```azurecli-interactive
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-system-node-pool-to-an-existing-aks-cluster"></a>Aggiungere un pool di nodi di sistema a un cluster AKS esistenteAdd a system node pool to an existing AKS cluster

È possibile aggiungere uno o più pool di nodi di sistema ai cluster AKS esistenti. Il comando seguente aggiunge un pool di nodi di tipo di modalità system con un conteggio predefinito di tre nodi.

```azurecli-interactive
az aks nodepool add -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```
## <a name="show-details-for-your-node-pool"></a>Visualizzare i dettagli per il pool di nodi

È possibile controllare i dettagli del pool di nodi con il comando seguente.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

Una modalità di tipo **Sistema** è definita per i pool di nodi di sistema e una modalità di tipo **Utente** è definita per i pool di nodi utente.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 3,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/666d66d8-1e43-4136-be25-f25bb5de5883/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/mynodepool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "mynodepool",
  "nodeLabels": {},
  "nodeTaints": null,
  "orchestratorVersion": "1.15.10",
  "osDiskSizeGb": 100,
  "osType": "Linux",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-system-and-user-node-pools"></a>Aggiornare i pool di nodi utente e di sistema

È possibile modificare le modalità per i pool di nodi di sistema e utente. È possibile modificare un pool di nodi di sistema in un pool di utenti solo se esiste già un altro pool di nodi di sistema nel cluster AKS.

Questo comando modifica un pool di nodi di sistema in un pool di nodi utente.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Questo comando modifica un pool di nodi utente in un pool di nodi di sistema.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Eliminare un pool di nodi di sistemaDelete a system node pool

> [!Note]
> Per utilizzare i pool di nodi di sistema nei cluster AKS prima della versione API 2020-03-02, aggiungere un nuovo pool di nodi di sistema, quindi eliminare il pool di nodi predefinito originale.

In precedenza non era possibile eliminare il pool di nodi di sistema, ovvero il pool di nodi predefinito iniziale in un cluster AKS. È ora possibile eliminare qualsiasi pool di nodi dai cluster. Poiché i cluster AKS richiedono almeno un pool di nodi di sistema, è necessario disporre di almeno due pool di nodi di sistema nel cluster AKS prima di poter eliminare uno di essi.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come creare e gestire pool di nodi di sistema in un cluster AKS. Per ulteriori informazioni sull'utilizzo di più pool di nodi, vedere [Utilizzare pool di più nodi.][use-multiple-node-pools]

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-delete
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/resource-group-using-tags.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: faq.md#why-cant-i-set-maxpods-below-30