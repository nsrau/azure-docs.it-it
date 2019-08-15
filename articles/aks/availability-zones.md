---
title: Usare zone di disponibilità in Azure Kubernetes Service (AKS)
description: Informazioni su come creare un cluster che distribuisce nodi tra le zone di disponibilità in Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: iainfou
ms.openlocfilehash: 52df1438913983a80ece487b49d0d6bd7821b636
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69019445"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Anteprima: creare un cluster Azure Kubernetes Service (AKS) che usa zone di disponibilità

Un cluster Azure Kubernetes Service (AKS) distribuisce risorse quali i nodi e l'archiviazione tra sezioni logiche dell'infrastruttura di calcolo di Azure sottostante. Questo modello di distribuzione assicura che i nodi vengano eseguiti in domini di aggiornamento e di errore distinti in un singolo Data Center di Azure. I cluster AKS distribuiti con questo comportamento predefinito forniscono un livello elevato di disponibilità per la protezione da un errore hardware o da un evento di manutenzione pianificata.

Per garantire un livello di disponibilità più elevato per le applicazioni, i cluster AKS possono essere distribuiti tra le zone di disponibilità. Queste zone sono centri dati fisicamente separati all'interno di una determinata area. Quando i componenti del cluster vengono distribuiti in più zone, il cluster AKS è in grado di tollerare un errore in una di queste zone. Le applicazioni e le operazioni di gestione continuano a essere disponibili anche se si è verificato un problema in un intero data center.

Questo articolo illustra come creare un cluster AKS e distribuire i componenti del nodo tra le zone di disponibilità. Questa funzionalità è attualmente in anteprima.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono self-service e acconsentino esplicitamente. Sono disponibili per raccogliere commenti e suggerimenti e bug dalla community. In anteprima, queste funzionalità non sono destinate all'uso in produzione. Le funzionalità nella versione di anteprima pubblica rientrano nel supporto "Best Effort". L'assistenza dei team di supporto tecnico AKS è disponibile solo durante l'orario di ufficio Pacific TimeZone (PST). Per ulteriori informazioni, consultare gli articoli di supporto seguenti:
>
> * [Criteri di supporto AKS][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.66 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [installare l'interfaccia][install-azure-cli]della riga di comando di Azure.

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per creare cluster AKS che usano le zone di disponibilità, è necessaria l'estensione dell'interfaccia della riga di comando *AKS-Preview* versione 0.4.1 o successiva. Installare l'estensione dell'interfaccia della riga di comando di Azure *AKS-Preview* usando il comando [AZ Extension Add][az-extension-add] , quindi verificare la presenza di eventuali aggiornamenti disponibili usando il comando [AZ Extension Update][az-extension-update] ::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>Registrare i flag delle funzionalità per la sottoscrizione

Per creare un cluster AKS con le zone di disponibilità, abilitare prima alcuni flag funzionalità per la sottoscrizione. I cluster usano un set di scalabilità di macchine virtuali per gestire la distribuzione e la configurazione dei nodi Kubernetes. Lo SKU *standard* del servizio di bilanciamento del carico di Azure è necessario anche per fornire resilienza ai componenti di rete per instradare il traffico al cluster. Registrare i flag delle funzionalità *AvailabilityZonePreview*, *AKSAzureStandardLoadBalancer*e *VMSSPreview* usando il comando [AZ feature Register][az-feature-register] , come illustrato nell'esempio seguente:

> [!CAUTION]
> Quando si registra una funzionalità in una sottoscrizione, attualmente non è possibile annullare la registrazione di tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, è possibile usare i valori predefiniti per tutti i cluster AKS, quindi creati nella sottoscrizione. Non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare una sottoscrizione separata per testare le funzionalità di anteprima e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato della registrazione usando il comando [AZ feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft. servizio contenitore* usando il comando [AZ provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>Limitazioni e disponibilità di aree

I cluster AKS possono essere attualmente creati usando le zone di disponibilità nelle aree seguenti:

* Stati Uniti orientali 2
* Europa settentrionale
* Asia sud-orientale
* Europa occidentale
* Stati Uniti occidentali 2

Quando si crea un cluster AKS usando le zone di disponibilità, si applicano le limitazioni seguenti:

* È possibile abilitare le zone di disponibilità solo quando viene creato il cluster.
* Non è possibile aggiornare le impostazioni della zona di disponibilità dopo la creazione del cluster. Non è inoltre possibile aggiornare un cluster di zone non di disponibilità esistente per usare le zone di disponibilità.
* Non è possibile disabilitare le zone di disponibilità per un cluster AKS dopo che è stato creato.
* Le dimensioni del nodo (SKU della VM) selezionate devono essere disponibili in tutte le zone di disponibilità.
* I cluster con le zone di disponibilità abilitate richiedono l'uso dei bilanciamento del carico standard di Azure per la distribuzione tra le zone.
* Per distribuire i servizio di bilanciamento del carico standard, è necessario usare Kubernetes versione 1.13.5 o successiva.

I cluster AKS che usano le zone di disponibilità devono usare lo SKU *standard* di Azure Load Balancer. Lo SKU *Basic* predefinito del servizio di bilanciamento del carico di Azure non supporta la distribuzione tra le zone di disponibilità. Per altre informazioni e per le limitazioni del servizio di bilanciamento del carico standard, vedere [limitazioni dell'anteprima dello SKU standard di Azure Load Balancer][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Limitazioni per i dischi di Azure

I volumi che usano Azure Managed Disks non sono al momento risorse di zona. I pod ripianificati in un'area diversa dalla zona originale non possono alleghi i dischi precedenti. Si consiglia di eseguire carichi di lavoro senza stato che non richiedano un'archiviazione permanente che può rientrare in problemi di zona.

Se è necessario eseguire carichi di lavoro con stato, usare tains e tollerations nelle specifiche pod per indicare all'utilità di pianificazione di Kubernetes di creare pod nella stessa zona dei dischi. In alternativa, usare l'archiviazione basata sulla rete, ad esempio File di Azure che può collegarsi ai pod così come sono pianificati tra le zone.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Panoramica di zone di disponibilità per i cluster AKS

Le zone di disponibilità offrono una soluzione a disponibilità elevata che consente di proteggere le applicazioni e i dati da eventuali guasti del data center. Le zone sono posizioni fisiche univoche all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati da eventuali guasti del data center. I servizi con ridondanza della zona replicano le applicazioni e i dati tra aree di disponibilità per garantire la protezione da singoli punti di errore.

Per ulteriori informazioni, vedere [che cosa sono zone di disponibilità in Azure?][az-overview].

I cluster AKS distribuiti usando le zone di disponibilità possono distribuire nodi in più zone all'interno di una singola area. Un cluster nell'area *Stati Uniti orientali 2* , ad esempio, può creare nodi in tutte e tre le zone di disponibilità nell'area *Stati Uniti orientali 2*. Questa distribuzione delle risorse del cluster AKS migliora la disponibilità del cluster perché sono resilienti agli errori di una zona specifica.

![Distribuzione del nodo AKS tra le zone di disponibilità](media/availability-zones/aks-availability-zones.png)

In un'interruzione di zona, i nodi possono essere ribilanciati manualmente o usando il ridimensionamento automatico del cluster. Se una singola zona diventa non disponibile, l'esecuzione delle applicazioni continua.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Creare un cluster AKS tra le zone di disponibilità

Quando si crea un cluster usando il comando [AZ AKS create][az-aks-create] , il `--node-zones` parametro definisce in quali zone vengono distribuiti i nodi agenti. I componenti del piano di controllo AKS per il cluster vengono distribuiti anche tra le zone con la configurazione più elevata disponibile quando si crea `--node-zones` un cluster che specifica il parametro.

Se non si definiscono zone per il pool di agenti predefinito quando si crea un cluster AKS, i componenti del piano di controllo AKS per il cluster non useranno le zone di disponibilità. È possibile aggiungere altri pool di nodi (attualmente in anteprima in AKS) usando il comando [AZ AKS nodepool Add][az-aks-nodepool-add] e `--node-zones` specificare per i nuovi nodi dell'agente, tuttavia i componenti del piano di controllo rimangono senza la consapevolezza della zona di disponibilità. Non è possibile modificare la conoscenza della zona per un pool di nodi o per i componenti del piano di controllo AKS dopo che sono stati distribuiti.

L'esempio seguente crea un cluster AKS denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*. Vengono creati un totale di *3* nodi: un agente nella zona *1*, uno in *2*e uno in *3*. I componenti del piano di controllo AKS vengono distribuiti anche tra le zone con la massima configurazione disponibile, perché sono definiti come parte del processo di creazione del cluster.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
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
