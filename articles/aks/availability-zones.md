---
title: Usare zone di disponibilità in Azure Kubernetes Service (AKS)
description: Informazioni su come creare un cluster che distribuisce nodi tra le zone di disponibilità in Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: mlearned
ms.openlocfilehash: 3790511bf3f71cdeb01853e4051a013719502d9f
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73605096"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Creare un cluster Azure Kubernetes Service (AKS) che usa zone di disponibilità

Un cluster Azure Kubernetes Service (AKS) distribuisce risorse quali i nodi e l'archiviazione tra sezioni logiche dell'infrastruttura di calcolo di Azure sottostante. Questo modello di distribuzione assicura che i nodi vengano eseguiti in domini di aggiornamento e di errore distinti in un singolo Data Center di Azure. I cluster AKS distribuiti con questo comportamento predefinito forniscono un livello elevato di disponibilità per la protezione da un errore hardware o da un evento di manutenzione pianificata.

Per garantire un livello di disponibilità più elevato per le applicazioni, i cluster AKS possono essere distribuiti tra le zone di disponibilità. Queste zone sono centri dati fisicamente separati all'interno di una determinata area. Quando i componenti del cluster vengono distribuiti in più zone, il cluster AKS è in grado di tollerare un errore in una di queste zone. Le applicazioni e le operazioni di gestione continuano a essere disponibili anche se si è verificato un problema in un intero data center.

Questo articolo illustra come creare un cluster AKS e distribuire i componenti del nodo tra le zone di disponibilità.

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.76 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

## <a name="limitations-and-region-availability"></a>Limitazioni e disponibilità di aree

I cluster AKS possono essere attualmente creati usando le zone di disponibilità nelle aree seguenti:

* Stati Uniti centrali
* Stati Uniti orientali 2
* Stati Uniti orientali
* Francia centrale
* Giappone orientale
* Europa settentrionale
* Asia sudorientale
* Regno Unito meridionale
* Europa occidentale
* Stati Uniti occidentali 2

Quando si crea un cluster AKS usando le zone di disponibilità, si applicano le limitazioni seguenti:

* È possibile abilitare le zone di disponibilità solo quando viene creato il cluster.
* Non è possibile aggiornare le impostazioni della zona di disponibilità dopo la creazione del cluster. Non è inoltre possibile aggiornare un cluster di zone non di disponibilità esistente per usare le zone di disponibilità.
* Non è possibile disabilitare le zone di disponibilità per un cluster AKS dopo che è stato creato.
* Le dimensioni del nodo (SKU della VM) selezionate devono essere disponibili in tutte le zone di disponibilità.
* I cluster con le zone di disponibilità abilitate richiedono l'uso dei bilanciamento del carico standard di Azure per la distribuzione tra le zone.
* Per distribuire i servizio di bilanciamento del carico standard, è necessario usare Kubernetes versione 1.13.5 o successiva.

I cluster AKS che usano le zone di disponibilità devono usare lo SKU *standard* di Azure Load Balancer, che corrisponde al valore predefinito per il tipo di servizio di bilanciamento del carico. Questo tipo di servizio di bilanciamento del carico può essere definito solo in fase di creazione del cluster. Per altre informazioni e per le limitazioni del servizio di bilanciamento del carico standard, vedere [limitazioni dello SKU standard di Azure Load Balancer][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Limitazioni per i dischi di Azure

I volumi che usano Azure Managed Disks non sono al momento risorse di zona. I pod ripianificati in un'area diversa dalla zona originale non possono alleghi i dischi precedenti. Si consiglia di eseguire carichi di lavoro senza stato che non richiedano un'archiviazione permanente che può rientrare in problemi di zona.

Se è necessario eseguire carichi di lavoro con stato, usare tains e tollerations nelle specifiche pod per indicare all'utilità di pianificazione di Kubernetes di creare pod nella stessa zona dei dischi. In alternativa, usare l'archiviazione basata sulla rete, ad esempio File di Azure che può collegarsi ai pod così come sono pianificati tra le zone.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Panoramica di zone di disponibilità per i cluster AKS

Le zone di disponibilità offrono una soluzione a disponibilità elevata che consente di proteggere le applicazioni e i dati da eventuali guasti del data center. Le zone sono posizioni fisiche univoche all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati da eventuali guasti del data center. I servizi con ridondanza della zona replicano le applicazioni e i dati tra aree di disponibilità per garantire la protezione da singoli punti di errore.

Per ulteriori informazioni, vedere [che cosa sono zone di disponibilità in Azure?][az-overview].

I cluster AKS distribuiti usando le zone di disponibilità possono distribuire nodi in più zone all'interno di una singola area. Ad esempio, un cluster nell'area *Stati Uniti orientali 2* può creare nodi in tutte e tre le zone di disponibilità nell'area *Stati Uniti orientali 2*. Questa distribuzione delle risorse del cluster AKS migliora la disponibilità del cluster perché sono resilienti agli errori di una zona specifica.

![Distribuzione del nodo AKS tra le zone di disponibilità](media/availability-zones/aks-availability-zones.png)

In un'interruzione di zona, i nodi possono essere ribilanciati manualmente o usando il ridimensionamento automatico del cluster. Se una singola zona diventa non disponibile, l'esecuzione delle applicazioni continua.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Creare un cluster AKS tra le zone di disponibilità

Quando si crea un cluster usando il comando [AZ AKS create][az-aks-create] , il `--zones` parametro definisce quali zone vengono distribuiti i nodi agenti. I componenti del piano di controllo AKS per il cluster vengono distribuiti anche tra le zone con la configurazione più elevata disponibile quando si definisce il parametro `--zones` al momento della creazione del cluster.

Se non si definiscono zone per il pool di agenti predefinito quando si crea un cluster AKS, i componenti del piano di controllo AKS per il cluster non useranno le zone di disponibilità. È possibile aggiungere altri pool di nodi usando il comando [AZ AKS nodepool Add][az-aks-nodepool-add] e specificare `--zones` per i nuovi nodi, tuttavia i componenti del piano di controllo rimangono senza la consapevolezza della zona di disponibilità. Non è possibile modificare la conoscenza della zona per un pool di nodi o per i componenti del piano di controllo AKS dopo che sono stati distribuiti.

L'esempio seguente crea un cluster AKS denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*. Vengono creati un totale di *3* nodi: un agente nella zona *1*, uno in *2*e uno in *3*. I componenti del piano di controllo AKS vengono distribuiti anche tra le zone con la massima configurazione disponibile, perché sono definiti come parte del processo di creazione del cluster.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

La creazione del cluster del servizio Azure Kubernetes richiede alcuni minuti.

## <a name="verify-node-distribution-across-zones"></a>Verificare la distribuzione del nodo tra le zone

Quando il cluster è pronto, elencare i nodi agente nel set di scalabilità per verificare la zona di disponibilità in cui sono distribuiti.

Per prima cosa, ottenere le credenziali del cluster AKS usando il comando [AZ AKS Get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Usare quindi il comando [kubectl Descrivi][kubectl-describe] per elencare i nodi del cluster. Filtrare sul valore *failure-domain.beta.kubernetes.io/zone* come illustrato nell'esempio seguente:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

L'output di esempio seguente mostra i tre nodi distribuiti nell'area specificata e nelle zone di disponibilità, ad esempio *eastus2-1* per la prima zona di disponibilità e *eastus2-2* per la seconda zona di disponibilità:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Quando si aggiungono altri nodi a un pool di agenti, la piattaforma Azure distribuisce automaticamente le macchine virtuali sottostanti nelle zone di disponibilità specificate.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra in modo dettagliato come creare un cluster AKS che usa le zone di disponibilità. Per altre considerazioni sui cluster a disponibilità elevata, vedere [procedure consigliate per la continuità aziendale e il ripristino di emergenza in AKS][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
