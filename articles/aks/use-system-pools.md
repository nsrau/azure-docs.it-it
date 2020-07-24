---
title: Usare i pool di nodi di sistema in Azure Kubernetes Service (AKS)
description: Informazioni su come creare e gestire i pool di nodi di sistema in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.openlocfilehash: 2994a616d60258e81cbd5a409690abc18538183a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87015528"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Gestire i pool di nodi di sistema in Azure Kubernetes Service (AKS)

In Azure Kubernetes Service (AKS) i nodi della stessa configurazione sono raggruppati in pool di *nodi*. I pool di nodi contengono le macchine virtuali sottostanti che eseguono le applicazioni. I pool di nodi di sistema e i pool di nodi utente sono due diverse modalità di pool di nodi per i cluster AKS. I pool di nodi di sistema servono lo scopo principale di ospitare i pod di sistema critici, ad esempio CoreDNS e tunnelfront. I pool di nodi utente servono lo scopo principale di ospitare i pod dell'applicazione. Tuttavia, i pod dell'applicazione possono essere pianificati nei pool di nodi di sistema se si vuole avere un solo pool nel cluster AKS. Ogni cluster AKS deve contenere almeno un pool di nodi di sistema con almeno un nodo.

> [!Important]
> Se si esegue un singolo pool di nodi di sistema per il cluster AKS in un ambiente di produzione, è consigliabile usare almeno tre nodi per il pool di nodi.

## <a name="before-you-begin"></a>Prima di iniziare

* È necessario aver installato e configurato l'interfaccia della riga di comando di Azure 2.3.1 o versione successiva. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster AKS che supportano i pool di nodi di sistema, si applicano le limitazioni seguenti.

* Vedere [quote, restrizioni sulle dimensioni delle macchine virtuali e disponibilità di aree in Azure Kubernetes Service (AKS)][quotas-skus-regions].
* Il cluster AKS deve essere compilato con i set di scalabilità di macchine virtuali come tipo di VM e il servizio di bilanciamento del carico dello SKU *standard* .
* Il nome di un pool di nodi può contenere solo caratteri alfanumerici minuscoli e deve iniziare con una lettera minuscola. Per i pool di nodi Linux, la lunghezza deve essere compresa tra 1 e 12 caratteri. Per i pool di nodi di Windows, la lunghezza deve essere compresa tra 1 e 6 caratteri.
* Per impostare una modalità del pool di nodi, è necessario usare una versione API di 2020-03-01 o successiva. I cluster creati nelle versioni API precedenti alla 2020-03-01 contengono solo pool di nodi utente, ma è possibile eseguirne la migrazione in modo che contengano pool di nodi di sistema seguendo la [procedura di aggiornamento del pool](#update-existing-cluster-system-and-user-node-pools).
* La modalità di un pool di nodi è una proprietà obbligatoria e deve essere impostata in modo esplicito quando si usano modelli ARM o chiamate API dirette.

## <a name="system-and-user-node-pools"></a>Pool di nodi di sistema e utente

Per un pool di nodi di sistema, AKS assegna automaticamente l'etichetta **kubernetes.Azure.com/mode: System** ai relativi nodi. Questo fa sì che AKS preferisca la pianificazione di pod di sistema nei pool di nodi che contengono questa etichetta. Questa etichetta non impedisce la pianificazione dei Pod dell'applicazione nei pool di nodi di sistema. Tuttavia, è consigliabile isolare i pod di sistema critici dai pod dell'applicazione per impedire che i pod di applicazione non configurati correttamente o non autorizzati uccidano accidentalmente i pod di sistema. È possibile applicare questo comportamento creando un pool di nodi di sistema dedicato. Usare il `CriticalAddonsOnly=true:NoSchedule` Taint per impedire che i pod dell'applicazione vengano pianificati nei pool di nodi di sistema.

I pool di nodi di sistema presentano le restrizioni seguenti:

* I pool di sistema osType devono essere Linux.
* I pool di nodi utente osType possono essere Linux o Windows.
* I pool di sistema devono contenere almeno un nodo e i pool di nodi utente possono contenere zero o più nodi.
* I pool di nodi di sistema richiedono uno SKU di VM di almeno 2 vCPU e 4 GB di memoria.
* I pool di nodi di sistema devono supportare almeno 30 POD come descritto dalla [formula valore minimo e massimo per i pod][maximum-pods].
* I pool di nodi spot richiedono pool di nodi utente.

Con i pool di nodi è possibile eseguire le operazioni seguenti:

* Creare un pool di nodi di sistema dedicato (preferire la pianificazione dei pod di sistema ai pool di nodi di `mode:system` )
* Modificare un pool di nodi di sistema in modo che sia un pool di nodi utente, purché si disponga di un altro pool di nodi di sistema da inserire nel cluster AKS.
* Modificare un pool di nodi utente in modo che sia un pool di nodi di sistema.
* Eliminare i pool di nodi utente.
* È possibile eliminare i pool di nodi di sistema, purché si disponga di un altro pool di nodi di sistema per sostituirlo nel cluster AKS.
* Un cluster AKS può avere più pool di nodi di sistema e richiede almeno un pool di nodi di sistema.
* Se si desidera modificare diverse impostazioni non modificabili nei pool di nodi esistenti, è possibile creare nuovi pool di nodi per sostituirli. Un esempio consiste nell'aggiungere un nuovo pool di nodi con una nuova impostazione maxPods ed eliminare il vecchio pool di nodi.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Creare un nuovo cluster AKS con un pool di nodi di sistema

Quando si crea un nuovo cluster AKS, viene creato automaticamente un pool di nodi di sistema con un solo nodo. Per impostazione predefinita, il pool di nodi iniziale è una modalità di tipo System. Quando si creano nuovi pool di nodi con, i pool di `az aks nodepool add` nodi sono pool di nodi utente a meno che non si specifichi in modo esplicito il parametro mode.

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Usare il comando [az aks create][az-aks-create] per creare un cluster del servizio Azure Kubernetes. Nell'esempio seguente viene creato un cluster denominato *myAKSCluster* con un pool di sistema dedicato contenente un nodo. Per i carichi di lavoro di produzione, assicurarsi di usare i pool di nodi di sistema con almeno tre nodi. Il completamento dell'operazione può richiedere alcuni minuti.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>Aggiungere un pool di nodi di sistema dedicato a un cluster AKS esistente

> [!Important]
> Non è possibile modificare i danneggiamenti del nodo tramite l'interfaccia della riga di comando dopo la creazione del pool di nodi.

È possibile aggiungere uno o più pool di nodi di sistema ai cluster AKS esistenti. È consigliabile pianificare i pod dell'applicazione nei pool di nodi utente e dedicare i pool di nodi di sistema solo ai pod di sistema critici. Ciò impedisce ai pod di applicazioni non autorizzati di terminare accidentalmente i pod di sistema. Applicare questo comportamento con il `CriticalAddonsOnly=true:NoSchedule` [Taint][aks-taints] per i pool di nodi di sistema. 

Il comando seguente consente di aggiungere un pool di nodi dedicato del sistema di tipi Mode con un conteggio predefinito di tre nodi.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode system
```
## <a name="show-details-for-your-node-pool"></a>Mostra i dettagli per il pool di nodi

È possibile controllare i dettagli del pool di nodi con il comando seguente.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

Per i pool di nodi di sistema è definita una modalità di tipo **System** e per i pool di nodi utente viene definita una modalità di tipo **User** . Per un pool di sistema, verificare che il Taint sia impostato `CriticalAddonsOnly=true:NoSchedule` su, in modo da impedire che i pod dell'applicazione vengano pianificati in questo pool di nodi.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Aggiornare i pool di nodi utente e di sistema cluster esistenti

> [!NOTE]
> Per impostare una modalità del pool di nodi di sistema, è necessario usare una versione API di 2020-03-01 o successiva. I cluster creati nelle versioni API precedenti alla 2020-03-01 contengono solo pool di nodi utente di conseguenza. Per ricevere le funzionalità del pool di nodi di sistema e i vantaggi nei cluster meno recenti, aggiornare la modalità dei pool di nodi esistenti con i comandi seguenti nella versione più recente dell'interfaccia della riga di comando di Azure.

È possibile modificare le modalità per i pool di nodi del sistema e dell'utente. È possibile modificare un pool di nodi di sistema in un pool di utenti solo se nel cluster AKS è già presente un altro pool di nodi di sistema.

Questo comando consente di modificare un pool di nodi di sistema in un pool di nodi utente.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Questo comando consente di modificare un pool di nodi utente in un pool di nodi di sistema.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Eliminare un pool di nodi di sistema

> [!Note]
> Per usare i pool di nodi di sistema nei cluster AKS prima dell'API versione 2020-03-02, aggiungere un nuovo pool di nodi di sistema, quindi eliminare il pool di nodi predefinito originale.

In precedenza non è stato possibile eliminare il pool di nodi di sistema, che era il pool di nodi predefinito iniziale in un cluster AKS. È ora possibile eliminare qualsiasi pool di nodi dai cluster. Poiché i cluster AKS richiedono almeno un pool di nodi di sistema, prima di poter eliminare uno di essi è necessario disporre di almeno due pool di nodi di sistema nel cluster AKS.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>Eseguire la pulizia delle risorse

Per eliminare il cluster, usare il comando [AZ Group Delete][az-group-delete] per eliminare il gruppo di risorse AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come creare e gestire i pool di nodi di sistema in un cluster AKS. Per altre informazioni su come usare più pool di nodi, vedere [usare pool di nodi multipli][use-multiple-node-pools].

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#schedule-pods-using-taints-and-tolerations
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
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/linux/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
