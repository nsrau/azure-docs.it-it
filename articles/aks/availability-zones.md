---
title: Usare le zone di disponibilità in Azure Kubernetes Service (AKS)
description: Informazioni su come creare un cluster che distribuisce i nodi tra le zone di disponibilità in Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 06/24/2019
ms.author: iainfou
ms.openlocfilehash: 0f99386aa9eeb75a990507e383c32412fb39eceb
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67840680"
---
# <a name="preview---create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Anteprima - creare un cluster Azure Kubernetes Service (AKS) che usa le zone di disponibilità

Un cluster Azure Kubernetes Service (AKS) consente di distribuire le risorse, ad esempio i nodi e risorse di archiviazione in sezioni logiche di Azure sottostante dell'infrastruttura di calcolo. Questo modello di distribuzione consente di eseguire i nodi nei domini di errore e di aggiornamento separati in un singolo Data Center di Azure. I cluster servizio contenitore di AZURE distribuiti con questo comportamento predefinito forniscono un elevato livello di disponibilità per la protezione da un errore hardware o evento di manutenzione pianificata.

Per fornire un livello superiore di disponibilità per le applicazioni, i cluster servizio contenitore di AZURE possono essere distribuiti tra zone di disponibilità. Queste zone sono Data Center fisicamente distinti all'interno di una determinata area. Quando i componenti cluster sono distribuiti in più zone, il cluster AKS è in grado di tollerare un errore in una di queste zone. Le applicazioni e operazioni di gestione continuano a essere disponibile anche se un intero Data Center ha riscontrato un problema.

Questo articolo illustra come creare un cluster del servizio contenitore di AZURE e distribuire i componenti del nodo tra zone di disponibilità. Questa funzionalità è attualmente in anteprima.

> [!IMPORTANT]
> Funzionalità di anteprima del servizio contenitore di AZURE sono self-service, fornire il consenso esplicito. Vengono fornite per raccogliere commenti e suggerimenti e bug dalla community. In fase di anteprima, queste funzionalità non sono destinate all'uso di produzione. Le funzionalità in anteprima pubblica rientrano nel supporto "best effort". Assistenza dai team di supporto tecnico di AKS è disponibile durante le ore lavorative Pacifico (PST) solo timezone. Per altre informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto servizio contenitore di AZURE][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

## <a name="before-you-begin"></a>Prima di iniziare

È necessario la CLI di Azure versione 2.0.66 o versione successiva installato e configurato. Eseguire  `az --version` per trovare la versione. Se è necessario installare o eseguire l'aggiornamento, vedere [installare CLI Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per creare i cluster servizio contenitore di AZURE che usano le zone di disponibilità, è necessario il *aks-preview* CLI versione dell'estensione 0.4.1 o versione successiva. Installare il *aks-preview* estensione di comando di Azure usando la [Aggiungi estensione az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] comando::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-feature-flags-for-your-subscription"></a>Registrare i flag funzionalità per la sottoscrizione

Per creare un cluster AKS che le zone di disponibilità, abilitare prima di tutto alcuni flag funzionalità per la sottoscrizione. I cluster usano un macchina virtuale set di scalabilità per gestire la distribuzione e la configurazione dei nodi Kubernetes. Il *standard* SKU del servizio di bilanciamento del carico di Azure è necessaria anche per garantire la resilienza per i componenti di rete per instradare il traffico nel cluster. Registrare il *AvailabilityZonePreview*, *AKSAzureStandardLoadBalancer*, e *VMSSPreview* i flag funzionalità tramite i [register funzionalità az][az-feature-register] seguente come mostrato nell'esempio seguente:

> [!CAUTION]
> Quando si registra una funzionalità in una sottoscrizione, attualmente non è possibile annullare la registrazione tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, potrebbero essere utilizzati i valori predefiniti per tutti i cluster AKS quindi creati nella sottoscrizione. Non abilitare la funzionalità di anteprima nella sottoscrizione di produzione. Usare una sottoscrizione separata per le funzionalità di anteprima di test e raccogliere commenti e suggerimenti.

```azurecli-interactive
az feature register --name AvailabilityZonePreview --namespace Microsoft.ContainerService
az feature register --name AKSAzureStandardLoadBalancer --namespace Microsoft.ContainerService
az feature register --name VMSSPreview --namespace Microsoft.ContainerService
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllarne lo stato di registrazione usando il [elenco delle funzionalità az][az-feature-list] comando:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AvailabilityZonePreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione dei *containerservice* provider di risorse usando la [register di az provider][az-provider-register] comando:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="limitations-and-region-availability"></a>Limitazioni e aree disponibili

Attualmente è possibile creare il cluster AKS usando le zone di disponibilità nelle aree seguenti:

* Stati Uniti orientali 2
* Europa settentrionale
* Asia sud-orientale
* Europa occidentale
* Stati Uniti occidentali 2

Le limitazioni seguenti si applicano quando si crea un cluster AKS usando le zone di disponibilità:

* Quando viene creato il cluster, è possibile abilitare solo le zone di disponibilità.
* Impossibile aggiornare le impostazioni di zona di disponibilità dopo la creazione del cluster. È anche possibile aggiornare un cluster di zona esistente, non di disponibilità per usare le zone di disponibilità.
* Dopo che è stato creato, non è possibile disabilitare le zone di disponibilità per un cluster AKS.
* Le dimensioni del nodo (SKU di VM) selezionata devono essere disponibile in tutte le zone di disponibilità.
* I cluster con le zone abilitate richiedono la disponibilità usano di Load Balancer Standard di Azure per la distribuzione in zone.
* È necessario usare Kubernetes versione 1.13.5 o versione successiva per distribuire servizi di bilanciamento del carico Standard.

I cluster servizio contenitore di AZURE che usano le zone di disponibilità devono usare Azure load balancer *standard* SKU. Il valore predefinito *base* SKU del servizio di bilanciamento del carico di Azure non supporta la distribuzione in zone di disponibilità. Per altre informazioni e le limitazioni dello standard di bilanciamento del caricano, vedere [limiti di anteprima di Azure load balancer standard SKU][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Limitazioni dei dischi di Azure

Volumi che usano Azure managed disks non sono attualmente le risorse di zona. I POD ripianificati in un'area diversa dalla rispettiva zona originale non è possibile ricollegare i dischi precedenti. È consigliabile eseguire carichi di lavoro senza stati che non richiedono l'archiviazione permanente che può entrare in zona problemi.

Se è necessario eseguire i carichi di lavoro con stati, utilizzare taints e tolerations nelle specifiche del pod per indicare l'utilità di pianificazione di Kubernetes per creare il POD nella stessa zona i dischi. In alternativa, usare l'archiviazione basata su rete, ad esempio file di Azure che possono collegare ai POD come loro programmazione tra zone.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Panoramica delle zone di disponibilità per AKS cluster

Le zone di disponibilità offrono una soluzione a disponibilità elevata che consente di proteggere le applicazioni e i dati da eventuali guasti del data center. Le zone sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati da eventuali guasti del data center. I servizi con ridondanza della zona replicano le applicazioni e i dati tra aree di disponibilità per garantire la protezione da singoli punti di errore.

Per altre informazioni, vedere [quali sono le zone di disponibilità in Azure?][az-overview].

Cluster servizio contenitore di AZURE distribuite usando le zone di disponibilità è possibile distribuire i nodi in più zone all'interno di una singola area. Ad esempio, un cluster nel *Stati Uniti orientali 2* area consente di creare nodi in tutti i tre zone di disponibilità in *Stati Uniti orientali 2*. Questa distribuzione di risorse del cluster servizio contenitore di AZURE consente di migliorare la disponibilità del cluster non appena vengono resiliente agli errori di una zona specifica.

![Distribuzione del nodo AKS tra zone di disponibilità](media/availability-zones/aks-availability-zones.png)

Un'interruzione di area, i nodi possono essere ribilanciati manualmente o tramite il ridimensionamento automatico del cluster. Se una singola zona non è più disponibile, le applicazioni continuano a eseguire.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Creare un cluster AKS tra zone di disponibilità

Quando si crea un cluster usando il [az aks create][az-aks-create] comando, il `--node-zones` parametro definisce quali nodi agente zone vengono distribuite in. I componenti di piano di controllo servizio contenitore di AZURE per il cluster vengono distribuiti anche le zone nella configurazione più elevata disponibile quando si crea un cluster che specifica il `--node-zones` parametro.

Se non si definisce alcuna zona per il pool di agenti predefinito quando si crea un cluster AKS, i componenti di piano di controllo servizio contenitore di AZURE per il cluster non usa le zone di disponibilità. È possibile aggiungere il pool di nodi aggiuntivi (attualmente in anteprima nel servizio contenitore di AZURE) usando il [aggiungere az aks nodepool][az-aks-nodepool-add] comando e specificare `--node-zones` per i nuovi nodi agente, tuttavia i componenti di piano di controllo rimangano senza zona di disponibilità consapevolezza. Non è possibile modificare l'orario per un pool di nodi o il servizio contenitore di AZURE controllare i componenti di piano dopo che vengono implementate.

L'esempio seguente crea un cluster del servizio contenitore di AZURE denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*. Totale *3* verranno creati i nodi, un agente nella zona *1*, uno in *2*e quindi uno nel *3*. I componenti di piano di controllo servizio contenitore di AZURE vengono distribuiti anche tra le zone di configurazione a disponibilità più elevata perché sono definiti come parte del cluster creare il processo.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version 1.13.5 \
    --generate-ssh-keys \
    --enable-vmss \
    --load-balancer-sku standard \
    --node-count 3 \
    --node-zones 1 2 3
```

La creazione del cluster del servizio Azure Kubernetes richiede alcuni minuti.

## <a name="verify-node-distribution-across-zones"></a>Verificare di distribuzione del nodo tra zone

Quando il cluster è pronto, i nodi agente nel set di scalabilità per vedere quali zona di disponibilità vengono distribuiti in un elenco.

Innanzitutto, ottenere le credenziali del cluster AKS usando il [az aks get-credentials][az-aks-get-credentials] comando:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Successivamente, usare il [kubectl descrivono][kubectl-describe] comando per elencare i nodi del cluster. Filtrare le *failure-domain.beta.kubernetes.io/zone* valore come illustrato nell'esempio seguente:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

L'output di esempio seguente mostra i tre nodi distribuiti tra l'area specificata e zone di disponibilità, ad esempio *eastus2-1* per la prima zona di disponibilità e *eastus2-2* per secondo zona di disponibilità:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Quando si aggiungono ulteriori nodi a un pool di agenti, la piattaforma Azure distribuisce automaticamente le macchine virtuali sottostanti tra le zone di disponibilità specificato.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo dettagliato come creare un cluster servizio contenitore di AZURE che usa le zone di disponibilità. Per altre considerazioni sui cluster a disponibilità elevata, vedere [procedure consigliate per il ripristino di emergenza e continuità aziendale nel servizio contenitore di AZURE][best-practices-bc-dr].

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
