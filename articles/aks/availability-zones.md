---
title: Usare le zone di disponibilità nel servizio Azure Kubernetes
description: Informazioni su come creare un cluster che distribuisce nodi tra le zone di disponibilità nel servizio Azure Kubernetes
services: container-service
ms.custom: fasttrack-edit, references_regions
ms.topic: article
ms.date: 08/13/2020
ms.openlocfilehash: 6bca57f799681a9f51b983da6f565bb465da8814
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88214250"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Creare un cluster del servizio Azure Kubernetes che usi le zone di disponibilità

Un cluster del servizio Azure Kubernetes distribuisce risorse quali nodi e archiviazione tra sezioni logiche dell'infrastruttura di Azure sottostante. Questo modello di distribuzione quando si usano le zone di disponibilità assicura che i nodi in una determinata zona di disponibilità siano fisicamente separati da quelli definiti in un'altra zona di disponibilità. I cluster del servizio Azure Kubernetes distribuiti con più zone di disponibilità configurate in un cluster forniscono un livello di disponibilità più elevato per la protezione da un errore hardware o da un evento di manutenzione pianificata.

Definendo i pool di nodi in un cluster in modo da estendersi su più zone, i nodi in un pool di nodi specificato possono continuare a funzionare anche se una singola zona si è ridotta. Le applicazioni possono continuare a essere disponibili anche se si verifica un errore fisico in un singolo data center, se orchestrato in modo da tollerare l'errore di un subset di nodi.

Questo articolo illustra come creare un cluster del servizio Azure Kubernetes e distribuire i componenti del nodo tra le zone di disponibilità.

## <a name="before-you-begin"></a>Prima di iniziare

È necessario che sia installata e configurata l'interfaccia della riga di comando di Azure 2.0.76 o versioni successive. Eseguire  `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere  [Installare l'interfaccia della riga di comando di Azure][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Limitazioni e disponibilità in base all'area geografica

I cluster del servizio Azure Kubernetes possono essere attualmente creati usando le zone di disponibilità nelle aree seguenti:

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

Quando si crea un cluster del servizio Azure Kubernetes usando le zone di disponibilità, si applicano le limitazioni seguenti:

* Quando viene creato il cluster o il pool di nodi, è possibile definire solo le zone di disponibilità.
* Non è possibile aggiornare le impostazioni della zona di disponibilità dopo la creazione del cluster. Non è inoltre possibile aggiornare un cluster di zone di non disponibilità esistente per usare le zone di disponibilità.
* Le dimensioni del nodo scelto (SKU della macchina virtuale) selezionate devono essere disponibili in tutte le zone di disponibilità selezionate.
* I cluster con le zone di disponibilità abilitate richiedono l'uso dei servizi di bilanciamento del carico standard di Azure per la distribuzione tra le zone. Questo tipo di servizio di bilanciamento del carico può essere definito solo in fase di creazione del cluster. Per altre informazioni e per le limitazioni del servizio di bilanciamento del carico standard, vedere [Limitazioni dello SKU standard di bilanciamento del carico di Azure][standard-lb-limitations].

### <a name="azure-disks-limitations"></a>Limitazioni per i dischi di Azure

I volumi che usano i dischi gestiti di Azure non sono al momento risorse con ridondanza della zona. I volumi non possono essere collegati tra le zone e devono avere un percorso condiviso nella stessa zona di un determinato nodo che ospita il pod di destinazione.

Se è necessario eseguire carichi di lavoro con stato, usare taint e tolleranze del pool di nodi nelle specifiche di pod per raggruppare la pianificazione di pod nella stessa zona dei dischi. In alternativa, usare l'archiviazione basata sulla rete, ad esempio File di Azure che può collegarsi ai pod così come sono pianificati tra le zone.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Panoramica delle zone di disponibilità per cluster del servizio Azure Kubernetes

Le zone di disponibilità offrono una soluzione a disponibilità elevata che consente di proteggere le applicazioni e i dati da eventuali guasti del data center. Le zone sono località fisiche esclusive all'interno di un'area di Azure. Ogni zona è costituita da uno o più data center dotati di impianti indipendenti per l'alimentazione, il raffreddamento e la connettività di rete. Per garantire la resilienza, sono presenti almeno tre zone separate in tutte le aree abilitate delle zone. La separazione fisica delle zone di disponibilità all'interno di un'area consente di proteggere le applicazioni e i dati da eventuali guasti del data center.

Per altre informazioni, vedere [Informazioni sulle zone di disponibilità di Azure][az-overview].

I cluster del servizio Azure Kubernetes distribuiti usando le zone di disponibilità possono distribuire nodi in più zone all'interno di una singola area. Ad esempio, un cluster nell'area  *Stati Uniti orientali 2* può creare nodi in tutte e tre le zone di disponibilità in *Stati Uniti orientali 2*. Questa distribuzione delle risorse cluster del servizio Azure Kubernetes migliora la disponibilità del cluster perché sono resilienti agli errori di una zona specifica.

![Distribuzione del nodo del servizio Azure Kubernetes tra le zone di disponibilità](media/availability-zones/aks-availability-zones.png)

Se una singola zona diventa non disponibile, le applicazioni continuano a essere eseguite se il cluster è distribuito in più zone.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Creare un cluster del servizio Azure Kubernetes tra le zone di disponibilità

Quando si crea un cluster usando il comando [az aks create][az-aks-create], il parametro `--zones` definisce in quali zone vengono distribuiti i nodi agenti. I componenti del piano di controllo, ad esempio etcd, vengono distribuiti in tre zone se si definisce il parametro `--zones` al momento della creazione del cluster. Le zone specifiche in cui sono distribuiti i componenti del piano di controllo sono indipendenti dalle zone esplicite selezionate per il pool di nodi iniziale.

Se non si definiscono zone per il pool di agenti predefinito quando si crea un cluster del servizio Azure Kubernetes, non è garantito che i componenti del piano di controllo vengano distribuiti tra le zone di disponibilità. È possibile aggiungere altri pool di nodi usando il comando [az aks nodepool add][az-aks-nodepool-add] e specificare `--zones` per i nuovi nodi, ma ciò non modificherà il modo in cui il piano di controllo è stato distribuito tra le zone. Le impostazioni della zona di disponibilità possono essere definite solo in fase di creazione del cluster o del pool di nodi.

L'esempio seguente crea un cluster del servizio Azure Kubernetes denominato *myAKSCluster* nel gruppo di risorse denominato *myResourceGroup*. Viene creato un totale di *3* nodi: un agente nella zona *1*, uno nella *2* e uno nella *3*.

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

Quando si decide la zona a cui deve appartenere un nuovo nodo, un pool di nodi del servizio Azure Kubernetes userà un [bilanciamento ottimale della zona offerto dai set di scalabilità di macchine virtuali di Azure sottostanti][vmss-zone-balancing]. Un determinato pool di nodi del servizio Azure Kubernetes viene considerato "bilanciato" se ogni zona del set di scalabilità ha lo stesso numero di macchine virtuali o +\-- 1 macchine virtuali in tutte le altre zone.

## <a name="verify-node-distribution-across-zones"></a>Verificare la distribuzione del nodo tra le zone

Quando il cluster è pronto, elencare i nodi agente nel set di scalabilità per verificare la zona di disponibilità in cui sono distribuiti.

Innanzitutto, ottenere le credenziali per il cluster del servizio Azure Kubernetes usando il comando [az aks get-credentials][az-aks-get-credentials]:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Usare quindi il comando [kubectl Descrivi][kubectl-describe] per elencare i nodi nel cluster e filtrare il valore *failure-domain.beta.kubernetes.io/zone* . L'esempio seguente è relativo a una shell bash.

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

Si noti che nelle versioni più recenti di Kubernetes (1.17.0 e versioni successive), il servizio Azure Kubernetes usa l'etichetta più recente `topology.kubernetes.io/zone` oltre alla `failure-domain.beta.kubernetes.io/zone` deprecata.

## <a name="verify-pod-distribution-across-zones"></a>Verificare la distribuzione del pod tra le zone

Come documentato in [Etichette note, annotazioni e taint][kubectl-well_known_labels], Kubernetes usa l'etichetta `failure-domain.beta.kubernetes.io/zone` per distribuire automaticamente i pod in un controller o un servizio di replica tra le diverse zone disponibili. Per eseguire il test, è possibile aumentare il cluster da 3 a 5 nodi per verificare la distribuzione del pod corretta:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

Al termine dell'operazione di ridimensionamento dopo alcuni minuti, il comando `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` in una shell bash dovrebbe fornire un output simile a questo esempio:

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

Sono ora disponibili due nodi aggiuntivi nelle zone 1 e 2. È possibile distribuire un'applicazione costituita da tre repliche. Si userà NGINX come esempio:

```console
kubectl run nginx --image=nginx --replicas=3
```

Quando si visualizzano i nodi in cui sono in esecuzione i pod, i pod sono in esecuzione nei nodi corrispondenti a tre diverse zone di disponibilità. Ad esempio, con il comando `kubectl describe pod | grep -e "^Name:" -e "^Node:"` in una shell bash si otterrà un output simile al seguente:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Come si può notare dall'output precedente, il primo pod viene eseguito sul nodo 0, che si trova nella zona di disponibilità `eastus2-1`. Il secondo pod viene eseguito sul nodo 2, che corrisponde a `eastus2-3`e il terzo nel nodo 4, in `eastus2-2`. Senza alcuna configurazione aggiuntiva, Kubernetes distribuisce correttamente i pod in tutte e tre le zone di disponibilità.

## <a name="next-steps"></a>Passaggi successivi

Questo articolo illustra in modo dettagliato come creare un cluster del servizio Azure Kubernetes che usa le zone di disponibilità. Per altre considerazioni sui cluster ad alta disponibilità, vedere le [Procedure consigliate per continuità aziendale e ripristino di emergenza nel servizio Azure Kubernetes][best-practices-bc-dr].

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
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/kubernetes-api/labels-annotations-taints/
