---
title: Usare i gruppi di posizionamento di prossimità per ridurre la latenza per i cluster di Azure Kubernetes Service (AKS)
description: Informazioni su come usare i gruppi di posizionamento di prossimità per ridurre la latenza per i carichi di lavoro del cluster AKS.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: fa81e293bc5e53a852bdb404f9e6d41c4297647b
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349036"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Ridurre la latenza con gruppi di posizionamento vicini

> [!Note]
> Quando si usano i gruppi di posizionamento prossimità su AKS, la condivisione percorso si applica solo ai nodi dell'agente. È stato migliorato il nodo a nodo e il corrispondente Pod ospitato alla latenza pod. La condivisione percorso non influisce sulla posizione del piano di controllo di un cluster.

Quando si distribuisce l'applicazione in Azure, la distribuzione di istanze di macchine virtuali (VM) tra aree o zone di disponibilità crea una latenza di rete, che può influisca sulle prestazioni complessive dell'applicazione. Un gruppo di posizionamento vicino è un raggruppamento logico usato per assicurarsi che le risorse di calcolo di Azure siano posizionate fisicamente tra loro. Alcune applicazioni come giochi, simulazioni ingegneristiche e trading ad alta frequenza (HFT) richiedono bassa latenza e attività che vengono completate rapidamente. Per gli scenari HPC (High Performance Computing) come questi, è consigliabile usare i [gruppi di posizionamento vicini](../virtual-machines/linux/co-location.md#proximity-placement-groups) per i pool di nodi del cluster.

## <a name="before-you-begin"></a>Prima di iniziare

Questo articolo richiede la versione 2,14 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

### <a name="limitations"></a>Limitazioni

* È possibile eseguire il mapping di un gruppo di posizionamento vicino a una zona di disponibilità.
* Un pool di nodi deve usare i set di scalabilità di macchine virtuali per associare un gruppo di posizionamento di prossimità.
* Un pool di nodi può associare un gruppo di posizionamento vicino al solo tempo di creazione del pool di nodi.

## <a name="node-pools-and-proximity-placement-groups"></a>Pool di nodi e gruppi di posizionamento di prossimità

La prima risorsa distribuita con un gruppo di posizionamento di prossimità si connette a una data center specifica. Le risorse aggiuntive distribuite con lo stesso gruppo di posizionamento di prossimità si trovano nello stesso data center. Una volta che tutte le risorse che usano il gruppo di posizionamento di prossimità sono state arrestate (deallocate) o eliminate, non sono più connesse.

* Molti pool di nodi possono essere associati a un singolo gruppo di posizionamento di prossimità.
* Un pool di nodi può essere associato a un solo gruppo di posizionamento di prossimità.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Configurare i gruppi di posizionamento di prossimità con le zone di disponibilità

> [!NOTE]
> Sebbene i gruppi di posizionamento di prossimità richiedano che un pool di nodi usi al massimo una zona di disponibilità, il [contratto di base per le VM di Azure del 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) è ancora attivo per le macchine virtuali in una singola zona.

I gruppi di posizionamento di prossimità sono un concetto di pool di nodi ed è associato a ogni singolo pool di nodi. L'uso di una risorsa PPG non ha alcun effetto sulla disponibilità del piano di controllo AKS. Questo può influisca sul modo in cui un cluster deve essere progettato con le zone. Per garantire la distribuzione di un cluster tra più zone, è consigliabile usare la struttura seguente.

* Eseguire il provisioning di un cluster con il primo pool di sistema utilizzando 3 zone e nessun gruppo di posizionamento di prossimità associato. In questo modo si garantisce che i pod di sistema si trovino in un pool di nodi dedicato che verrà distribuito tra più zone.
* Aggiungere altri pool di nodi utente con una zona univoca e un gruppo di posizionamento di prossimità associato a ogni pool. Un esempio è nodepool1 nella zona 1 e PPG1, nodepool2 nella zona 2 e PPG2, nodepool3 nella zona 3 con PPG3. Ciò garantisce a livello di cluster che i nodi vengono distribuiti in più zone e ogni singolo pool di nodi si trova nella zona designata con una risorsa PPG dedicata.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Creare un nuovo cluster AKS con un gruppo di posizionamento di prossimità

Nell'esempio seguente viene usato il comando [AZ Group create][az-group-create] per creare un gruppo di risorse denominato *myResourceGroup* nell'area *centralus* . Un cluster AKS denominato *myAKSCluster* viene quindi creato usando il comando [AZ AKS create][az-aks-create] .

La rete accelerata migliora notevolmente le prestazioni di rete delle macchine virtuali. Idealmente, usare i gruppi di posizionamento di prossimità insieme alla rete accelerata. Per impostazione predefinita, AKS usa la rete accelerata nelle [istanze di macchina virtuale supportate](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), che includono la maggior parte delle macchine virtuali di Azure con due o più vCPU.

Creare un nuovo cluster AKS con un gruppo di posizionamento di prossimità associato al primo pool di nodi di sistema:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Eseguire il comando seguente e archiviare l'ID restituito:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

Il comando genera l'output, che include il valore *ID* necessario per i successivi comandi dell'interfaccia della riga di comando:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Usare l'ID di risorsa del gruppo di posizionamento vicino per il valore *myPPGResourceID* nel comando seguente:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Aggiungere un gruppo di posizionamento vicino a un cluster esistente

È possibile aggiungere un gruppo di posizionamento vicino a un cluster esistente creando un nuovo pool di nodi. È quindi possibile eseguire facoltativamente la migrazione dei carichi di lavoro esistenti al nuovo pool di nodi, quindi eliminare il pool di nodi originale.

Usare lo stesso gruppo di posizionamento di prossimità creato in precedenza, in modo che i nodi agente in entrambi i pool di nodi del cluster AKS si trovino fisicamente nello stesso data center.

Usare l'ID risorsa del gruppo di posizionamento vicino creato in precedenza e aggiungere un nuovo pool di nodi con il [`az aks nodepool add`][az-aks-nodepool-add] comando:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Eseguire la pulizia

Per eliminare il cluster, usare il [`az group delete`][az-group-delete] comando per eliminare il gruppo di risorse AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sui [gruppi di posizionamento di prossimità][proximity-placement-groups].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/linux/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
