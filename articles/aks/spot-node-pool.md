---
title: 'Anteprima: aggiungere un pool di nodi spot a un cluster di Azure Kubernetes Service (AKS)'
description: Informazioni su come aggiungere un pool di nodi spot a un cluster di Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 02/25/2020
ms.author: zarhoads
ms.openlocfilehash: 466ad7c88547b6676ba0ae263b74d14059322f1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77622053"
---
# <a name="preview---add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Anteprima: aggiungere un pool di nodi spot a un cluster di Azure Kubernetes Service (AKS)

Un pool di nodi spot è un pool di nodi supportato da un set di [scalabilità di macchine virtuali spot.][vmss-spot] L'uso di macchine virtuali a compensi per i nodi con il cluster AKS consente di sfruttare la capacità non utilizzata in Azure con un notevole risparmio sui costi. La quantità di capacità non utilizzata disponibile varia in base a molti fattori, tra cui la dimensione del nodo, l'area e l'ora del giorno.

Quando si distribuisce un pool di nodi spot, Azure allocherà i nodi spot se è disponibile capacità. Ma non c'è nessun sLA per i nodi spot. Un set di scalabilità spot che supporta il pool di nodi spot viene distribuito in un singolo dominio di errore e non offre garanzie di disponibilità elevata. In qualsiasi momento in cui Azure richiede la capacità, l'infrastruttura di Azure rimuovere i nodi spot.

I nodi spot sono ideali per i carichi di lavoro in grado di gestire interruzioni, terminazioni anticipate o sfratti. Ad esempio, carichi di lavoro come processi di elaborazione batch, ambienti di sviluppo e test e carichi di lavoro di calcolo di grandi dimensioni possono essere buoni candidati per essere pianificati in un pool di nodi spot.

In questo articolo si aggiunge un pool di nodi spot secondario a un cluster di servizi Azure Kubernetes (AKS) esistente.

Questo articolo presuppone una conoscenza di base dei concetti di Kubernetes e Azure Load Balancer.This article assumes a basic understanding of Kubernetes and Azure Load Balancer concepts. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

Questa funzionalità è attualmente in anteprima.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Quando si crea un cluster per usare un pool di nodi spot, tale cluster deve utilizzare anche i set di scalabilità di macchine virtuali per i pool di nodi e il servizio di bilanciamento del carico SKU *Standard.When* you create a cluster to use a spot node pool, that cluster also use Virtual Machine Scale Sets for node pools and the Standard SKU load balancer. È inoltre necessario aggiungere un pool di nodi aggiuntivo dopo aver creato il cluster per utilizzare un pool di nodi spot. L'aggiunta di un pool di nodi aggiuntivo viene descritta in un passaggio successivo, ma è innanzitutto necessario abilitare una funzionalità di anteprima.

> [!IMPORTANT]
> Le funzionalità di anteprima di AKS sono self-service, opt-in. Sono forniti per raccogliere feedback e bug dalla nostra comunità. Nell'anteprima, queste funzionalità non sono destinate all'uso in produzione. Le funzionalità in anteprima pubblica sono sottoposte al supporto "best effort". L'assistenza dei team di supporto tecnico AKS è disponibile solo durante il fuso orario del Pacifico (PST) dell'orario di ufficio. Per ulteriori informazioni, vedere i seguenti articoli di supporto:
>
> * [Criteri di supporto AKS][aks-support-policies]
> * [Domande frequenti relative al supporto tecnico Azure][aks-faq]

### <a name="register-spotpoolpreview-preview-feature"></a>Registrare la funzione di anteprima di spotpoolpreview

Per creare un cluster AKS che utilizza un pool di nodi spot, è necessario abilitare il flag della funzionalità *spotpoolpreview* nella sottoscrizione. Questa funzionalità fornisce il set più recente di miglioramenti del servizio durante la configurazione di un cluster.

> [!CAUTION]
> Quando si registra una funzionalità in un abbonamento, al momento non è possibile annullare la registrazione di tale funzionalità. Dopo aver abilitato alcune funzionalità di anteprima, le impostazioni predefinite possono essere utilizzate per tutti i cluster AKS quindi creati nella sottoscrizione. Non abilitare le funzionalità di anteprima nelle sottoscrizioni di produzione. Usare un abbonamento separato per testare le funzionalità di anteprima e raccogliere commenti e suggerimenti.

Registrare il flag della funzionalità *spotpoolpreview* utilizzando il comando [az feature register][az-feature-register] come illustrato nell'esempio seguente:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "spotpoolpreview"
```

Sono necessari alcuni minuti per visualizzare lo stato *Registered*. È possibile controllare lo stato di registrazione usando il comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/spotpoolpreview')].{Name:name,State:properties.state}"
```

Quando si è pronti, aggiornare la registrazione del provider di risorse *Microsoft.ContainerService* usando il comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="install-aks-preview-cli-extension"></a>Installare l'estensione dell'interfaccia della riga comando di aks-preview

Per creare un cluster AKS che usa un pool di nodi spot, è necessaria l'estensione dell'interfaccia della riga di comando *aks-preview* versione 0.4.32 o successiva. Installare l'estensione aks-preview di Azure CLI usando il comando [az extension add,][az-extension-add] quindi verificare la presenza di eventuali aggiornamenti disponibili usando il comando [az extension update:Install][az-extension-update] the *aks-preview* Azure CLI extension using the az extension add command, then check for any available updates using the az extension update command:

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview
 
# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono cluster AKS con un pool di nodi spot, si applicano le limitazioni seguenti:The following limitations apply when you create and manage AKS clusters with a spot node pool:

* Un pool di nodi spot non può essere il pool di nodi predefinito del cluster. Un pool di nodi spot può essere utilizzato solo per un pool secondario.
* Non è possibile aggiornare un pool di nodi spot poiché i pool di nodi spot non possono garantire cordone e scarico. È necessario sostituire il pool di nodi spot esistente con uno nuovo per eseguire operazioni come l'aggiornamento della versione Kubernetes. Per sostituire un pool di nodi spot, creare un nuovo pool di nodi spot con una versione diversa di Kubernetes, attendere che il relativo stato sia *Pronto*, quindi rimuovere il pool di nodi precedente.
* Il piano di controllo e i pool di nodi non possono essere aggiornati contemporaneamente. È necessario aggiornarli separatamente o rimuovere il pool di nodi spot per aggiornare contemporaneamente il piano di controllo e i pool di nodi rimanenti.
* Un pool di nodi spot deve utilizzare i set di scalabilità di macchine virtuali.
* Non è possibile modificare ScaleSetPriority o SpotMaxPrice dopo la creazione.
* Quando si imposta SpotMaxPrice, il valore deve essere -1 o un valore positivo con un massimo di cinque posizioni decimali.
* Un pool di nodi spot avrà l'etichetta *kubernetes.azure.com/scalesetpriority:spot*, la taint *kubernetes.azure.com/scalesetpriority=spot:NoSchedule*e i pod di sistema avranno anti-affinità.
* È necessario aggiungere una [torazione corrispondente][spot-toleration] per pianificare i carichi di lavoro in un pool di nodi spot.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Aggiungere un pool di nodi spot a un cluster AKSAdd a spot node pool to an AKS cluster

È necessario aggiungere un pool di nodi spot a un cluster esistente con più pool di nodi abilitati. Ulteriori dettagli sulla creazione di un cluster AKS con pool a più nodi sono disponibili [qui.][use-multiple-node-pools]

Creare un pool di nodi utilizzando l'aggiunta di [nodepool az aks][az-aks-nodepool-add].
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Per impostazione predefinita, si crea un pool *di* nodi con priorità *Regular* nel cluster AKS quando si crea un cluster con più pool di nodi. Il comando precedente aggiunge un pool di nodi ausiliari a un cluster AKS esistente con *priorità* *Spot*. La *priorità* di *Spot* rende il pool di nodi un pool di nodi spot. Il parametro *sviction-policy* è impostato su *Delete* nell'esempio precedente, che è il valore predefinito. Quando si imposta il criterio di [rimozione][eviction-policy] *su Delete*, i nodi nel set di scalabilità sottostante del pool di nodi vengono eliminati quando vengono rimossi. È inoltre possibile impostare i criteri di rimozione su *Deallocate*. Quando si imposta il criterio di rimozione su *Deal ,* i nodi nel set di scalabilità sottostante vengono impostati sullo stato di deallocazione arrestato al momento dello sfratto. I nodi nello stato arrestato-deallocato vengono conteggiati rispetto alla quota di calcolo e possono causare problemi con il ridimensionamento o l'aggiornamento del cluster. I valori dei criteri *di priorità* e *di rimozione* possono essere impostati solo durante la creazione del pool di nodi. Tali valori non possono essere aggiornati in un secondo momento.

Il comando abilita inoltre il [cluster autoscaler][cluster-autoscaler], che è consigliabile utilizzare con i pool di nodi spot. In base ai carichi di lavoro in esecuzione nel cluster, il cluster autoscaler scala e riduce il numero di nodi nel pool di nodi. Per i pool di nodi spot, il cluster autoscaler aumenta il numero di nodi dopo uno sfratto se sono ancora necessari nodi aggiuntivi. Se si modifica il numero massimo di nodi che un `maxCount` pool di nodi può avere, è necessario modificare anche il valore associato al cluster autoscaler. Se non si utilizza un cluster autoscaler, al momento dello sfratto, il pool spot alla fine diminuirà a zero e richiederà un'operazione manuale per ricevere eventuali nodi spot aggiuntivi.

> [!Important]
> Pianificare i carichi di lavoro solo nei pool di nodi spot in grado di gestire le interruzioni, ad esempio i processi di elaborazione batch e gli ambienti di test. È consigliabile impostare [taint e tolarazioni][taints-tolerations] nel pool di nodi spot per garantire che solo i carichi di lavoro in grado di gestire gli sfratti dei nodi siano pianificati in un pool di nodi spot. Ad esempio, il comando precedente ny default aggiunge una macchia di *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* in modo che solo i pod con una torazione corrispondente siano pianificati su questo nodo.

## <a name="verify-the-spot-node-pool"></a>Verificare il pool di nodi spot

Per verificare che il pool di nodi sia stato aggiunto come pool di nodi spot:To verify your node pool has been added as a spot node pool:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Verificare che *scaleSetPriority* sia *Spot*.

Per pianificare l'esecuzione di un contenitore su un nodo spot, aggiungere una torazione che corrisponde alla macchia applicata al nodo spot. Nell'esempio seguente viene illustrata una parte di un file yaml che definisce una torazione che corrisponde a una *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* taint utilizzata nel passaggio precedente.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

Quando viene distribuito un baccello con questa torazione, Kubernetes può pianificare correttamente il pod sui nodi con la macchia applicata.

## <a name="max-price-for-a-spot-pool"></a>Prezzo massimo per una piscina spot
[Il prezzo per le istanze spot è variabile,][pricing-spot]in base all'area e all'UsK. Per ulteriori informazioni, vedere Prezzi per [Linux][pricing-linux] e [Windows][pricing-windows].

Con prezzi variabili, hai la possibilità di impostare un prezzo massimo, in dollari USA (USD), utilizzando fino a 5 cifre decimali. Ad esempio, il valore *0,98765* sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il prezzo massimo su *-1*, l'istanza non verrà rimossa in base al prezzo. Il prezzo per l'istanza sarà il prezzo corrente per Spot o il prezzo per un'istanza standard, a seconda di quale sia minore, purché vi siano capacità e quote disponibili.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come aggiungere un pool di nodi spot a un cluster AKS. Per ulteriori informazioni su come controllare i pod tra i pool di nodi, consultate [Procedure consigliate per le funzionalità avanzate dell'utilità di pianificazione in AKS.][operator-best-practices-advanced-scheduler]

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-deploy-create]: /cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-template-deploy]: ../azure-resource-manager/templates/deploy-cli.md#deployment-scope
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md