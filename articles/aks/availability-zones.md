---
title: Usare le zone di disponibilità nel servizio Azure Kubernetes (AKS)Use availability zones in Azure Kubernetes Service (AKS)
description: Informazioni su come creare un cluster che distribuisce i nodi tra le zone di disponibilità nel servizio Azure Kubernetes (AKS)Learn how to create a cluster that distributes nodes across availability zones in Azure Kubernetes Service (AKS)
services: container-service
ms.custom: fasttrack-edit
ms.topic: article
ms.date: 06/24/2019
ms.openlocfilehash: 5693d9e90de9ba68e7b76e0f2bd5b75141dbda71
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77596811"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Creare un cluster del servizio Azure Kubernetes (AKS) che usa le zone di disponibilitàCreate an Azure Kubernetes Service (AKS) cluster that uses availability zones

Un cluster di servizio Azure Kubernetes (AKS) distribuisce le risorse, ad esempio i nodi e l'archiviazione tra sezioni logiche dell'infrastruttura di calcolo di Azure sottostante. Questo modello di distribuzione assicura che i nodi vengano eseguiti in domini di aggiornamento ed errore separati in un singolo data center di Azure.This deployment model makes sure that the nodes run across separate update and fault domains in a single Azure datacenter. I cluster AKS distribuiti con questo comportamento predefinito forniscono un livello elevato di disponibilità da proteggere da un errore hardware o da un evento di manutenzione pianificato.

Per fornire un livello più elevato di disponibilità alle applicazioni, i cluster AKS possono essere distribuiti tra le zone di disponibilità. Queste zone sono data center fisicamente separati all'interno di una determinata area. Quando i componenti del cluster vengono distribuiti in più zone, il cluster AKS è in grado di tollerare un errore in una di queste zone. Le applicazioni e le operazioni di gestione continuano a essere disponibili anche se si verifica un problema con un intero data center.

In questo articolo viene illustrato come creare un cluster AKS e distribuire i componenti del nodo tra le zone di disponibilità.

## <a name="before-you-begin"></a>Prima di iniziare

È necessaria l'interfaccia della riga di comando di Azure versione 2.0.76 o successiva installata e configurata. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Limitazioni e disponibilità dell'area geograficaLimitations and region availability

I cluster AKS possono attualmente essere creati usando le zone di disponibilità nelle aree seguenti:AKS clusters can currently be created using availability zones in the following regions:

* Stati Uniti centrali
* Stati Uniti orientali 2
* Stati Uniti orientali
* Francia centrale
* Giappone orientale
* Europa settentrionale
* Asia sud-orientale
* Regno Unito meridionale
* Europa occidentale
* Stati Uniti occidentali 2

Quando si crea un cluster AKS usando le zone di disponibilità, si applicano le limitazioni seguenti:The following limitations apply when you create an AKS cluster using availability zones:

* È possibile abilitare le zone di disponibilità solo quando viene creato il cluster.
* Le impostazioni della zona di disponibilità non possono essere aggiornate dopo la creazione del cluster. Non è inoltre possibile aggiornare un cluster di zone a disponibilità esistente per l'utilizzo delle zone di disponibilità.
* Non è possibile disabilitare le zone di disponibilità per un cluster AKS dopo che è stato creato.
* La dimensione del nodo (SKU VM) selezionata deve essere disponibile in tutte le zone di disponibilità.
* I cluster con zone di disponibilità abilitate richiedono l'uso di servizi di bilanciamento del carico standard di Azure per la distribuzione tra le zone.
* Per distribuire i servizi di bilanciamento del carico standard, è necessario usare Kubernetes versione 1.13.5 o successiva.

I cluster AKS che usano le zone di disponibilità devono usare lo SKU *standard* del servizio di bilanciamento del carico di Azure, che è il valore predefinito per il tipo di bilanciamento del carico. Questo tipo di bilanciamento del carico può essere definito solo in fase di creazione del cluster. Per altre informazioni e le limitazioni del servizio di bilanciamento del carico standard, vedere Limitazioni dello SKU standard del servizio di bilanciamento del carico di Azure.For more information and the limitations of the standard load balancer, see [Azure load balancer standard SKU limitations][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Limitazioni dei dischi di AzureAzure disks limitations

I volumi che usano dischi gestiti di Azure non sono attualmente risorse di zona. I pod riprogrammati in una zona diversa dalla zona originale non possono ricollegare i dischi precedenti. È consigliabile eseguire carichi di lavoro senza stato che non richiedono uno spazio di archiviazione permanente che può verificarsi in problemi di zona.

Se è necessario eseguire carichi di lavoro con stato, utilizzare taint sbarramenti e torazioni nelle specifiche del pod per indicare all'utilità di pianificazione Kubernetes di creare pod nella stessa zona dei dischi. In alternativa, usare l'archiviazione basata sulla rete, ad esempio File di Azure, che possono connettersi ai pod mentre vengono pianificati tra le zone.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Panoramica delle zone di disponibilità per i cluster AKS

Le zone di disponibilità sono un'offerta a disponibilità elevata che protegge le applicazioni e i dati dagli errori del data center. Le zone sono posizioni fisiche univoche all'interno di un'area di Azure.zones are are unique physical locations within an Azure region. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati da eventuali guasti del data center. I servizi con ridondanza di zona replicano le applicazioni e i dati tra le zone di disponibilità per proteggersi da singoli punti di errore.

Per altre informazioni, vedere Che cosa sono le zone di [disponibilità in Azure?][az-overview].

I cluster AKS distribuiti tramite zone di disponibilità possono distribuire nodi tra più zone all'interno di una singola area. Ad esempio, un cluster nell'area *Stati Uniti orientali 2* può creare nodi in tutte e tre le zone di disponibilità *negli Stati Uniti orientali 2*. Questa distribuzione delle risorse cluster AKS migliora la disponibilità del cluster in quanto sono resilienti agli errori di una zona specifica.

![Distribuzione dei nodi AKS tra le zone di disponibilitàAKS node distribution across availability zones](media/availability-zones/aks-availability-zones.png)

In un'interruzione di zona, i nodi possono essere ribilanciati manualmente o utilizzando il cluster autoscaler. Se una singola zona non è più disponibile, le applicazioni continuano a essere eseguite.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Creare un cluster AKS tra le zone di disponibilitàCreate an AKS cluster across availability zones

Quando si crea un cluster utilizzando il `--zones` comando [az aks create,][az-aks-create] il parametro definisce in quali nodi dell'agente zone vengono distribuiti. I componenti del piano di controllo AKS per il cluster vengono `--zones` distribuiti tra le zone nella configurazione più alta disponibile quando si definisce il parametro al momento della creazione del cluster.

Se non si definisce zone per il pool di agenti predefinito quando si crea un cluster AKS, i componenti del piano di controllo AKS per il cluster non utilizzeranno le zone di disponibilità. È possibile aggiungere altri pool di nodi utilizzando `--zones` il comando [az aks nodepool add][az-aks-nodepool-add] e specificare per i nuovi nodi, tuttavia i componenti del piano di controllo rimangono senza riconoscimento della zona di disponibilità. Non è possibile modificare il riconoscimento della zona per un pool di nodi o i componenti del piano di controllo AKS dopo la distribuzione.

Nell'esempio seguente viene creato un cluster AKS denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*. Vengono creati un totale di *3* nodi, ovvero un agente nella zona *1,* uno in *2*e quindi uno in *3*. Anche i componenti del piano di controllo AKS vengono distribuiti tra le zone nella configurazione più elevata disponibile poiché sono definiti come parte del processo di creazione del cluster.

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

## <a name="verify-node-distribution-across-zones"></a>Verificare la distribuzione dei nodi tra le zoneVerify node distribution across zones

Quando il cluster è pronto, elencare i nodi agente nel set di scalabilità per vedere in quale zona di disponibilità sono distribuiti.

In primo luogo, ottenere le credenziali del cluster AKS utilizzando il comando [az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Successivamente, usare il comando [kubectl describe][kubectl-describe] per elencare i nodi del cluster. Filtrare in base al valore *failure-domain.beta.kubernetes.io/zone,* come illustrato nell'esempio seguente:

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

L'output di esempio seguente mostra i tre nodi distribuiti nell'area specificata e nelle zone di disponibilità, ad esempio eastus2-1 per la prima zona di disponibilità e eastus2-2 per la seconda zona di disponibilità:The following example output shows the three nodes distributed across the specified region and availability zones, such as *eastus2-1* for the first availability zone and *eastus2-2* for the second availability zone:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

Quando si aggiungono altri nodi a un pool di agenti, la piattaforma Azure distribuisce automaticamente le macchine virtuali sottostanti tra le zone di disponibilità specificate.

Si noti che nelle versioni più recenti di Kubernetes (1.17.0 e versioni successive), AKS utilizza l'etichetta `topology.kubernetes.io/zone` più recente oltre alla versione deprecata. `failure-domain.beta.kubernetes.io/zone`

## <a name="verify-pod-distribution-across-zones"></a>Verificare la distribuzione dei pod tra le zone

Come documentato in [Etichette, Annotazioni e Taint ben noti,][kubectl-well_known_labels]Kubernetes utilizza l'etichetta `failure-domain.beta.kubernetes.io/zone` per distribuire automaticamente i pod in un controller o servizio di replica tra le diverse zone disponibili. Per verificare questo test, è possibile scalare il cluster da 3 a 5 nodi, per verificare la corretta diffusione del pod:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Quando l'operazione di ridimensionamento viene `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` completata dopo alcuni minuti, il comando deve fornire un output simile a questo esempio:When the scale operation completes after few minutes, the command should give an output similar to this sample:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Come potete vedere, ora abbiamo due nodi aggiuntivi nelle zone 1 e 2. È possibile distribuire un'applicazione costituita da tre repliche. Useremo NGINX come esempio:

```console
kubectl run nginx --image=nginx --replicas=3
```

Se verificate che i nodi in cui sono in esecuzione i pod, vedrete che i pod sono in esecuzione sui pod corrispondenti a tre diverse zone di disponibilità. Ad esempio, `kubectl describe pod | grep -e "^Name:" -e "^Node:"` con il comando si otterrebbe un output simile al seguente:For example with the command you would get an output similar to this:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Come si può vedere dall'output precedente, il primo pod è in `eastus2-1`esecuzione sul nodo 0, che si trova nella zona di disponibilità . Il secondo pod è in esecuzione `eastus2-3`sul nodo 2, che corrisponde `eastus2-2`a , e il terzo nel nodo 4, in . Senza alcuna configurazione aggiuntiva, Kubernetes sta diffondendo correttamente i pod in tutte e tre le zone di disponibilità.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo viene descritto in dettaglio come creare un cluster AKS che utilizza le zone di disponibilità. Per altre considerazioni sui cluster a disponibilità elevata, vedere Procedure consigliate per la continuità aziendale e il [ripristino di emergenza in AKS][best-practices-bc-dr].

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
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
