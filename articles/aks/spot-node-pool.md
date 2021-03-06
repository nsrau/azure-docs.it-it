---
title: Aggiungere un pool di nodi spot a un cluster del servizio Azure Kubernetes (AKS)
description: Informazioni su come aggiungere un pool di nodi spot a un cluster di Azure Kubernetes Service (AKS).
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: 5fd97560c3a6e41b49beb957c7b8d79369799c21
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078952"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Aggiungere un pool di nodi spot a un cluster del servizio Azure Kubernetes (AKS)

Un pool di nodi spot è un pool di nodi supportato da un [set di scalabilità di macchine virtuali][vmss-spot]. L'uso di VM spot per i nodi con il cluster AKS consente di sfruttare i vantaggi della capacità non utilizzata in Azure con un notevole risparmio sui costi. La quantità di capacità disponibile non utilizzata può variare in base a molti fattori, tra cui le dimensioni del nodo, l'area e l'ora del giorno.

Quando si distribuisce un pool di nodi spot, Azure alloca i nodi spot se è disponibile la capacità. Ma non esiste alcun contratto di contratto per i nodi spot. Un set di scalabilità spot che esegue il backup del pool di nodi spot viene distribuito in un singolo dominio di errore e non offre garanzie di disponibilità elevata. In qualsiasi momento, quando Azure necessita della capacità, l'infrastruttura di Azure rimuoverà i nodi spot.

I nodi spot sono ottimi per i carichi di lavoro in grado di gestire le interruzioni, le chiusure anticipate o le eliminazioni. Ad esempio, i carichi di lavoro come i processi di elaborazione batch, gli ambienti di sviluppo e di test e i carichi di lavoro di calcolo di grandi dimensioni possono essere candidati ideali per essere pianificati in un pool di nodi spot.

In questo articolo si aggiunge un pool di nodi spot secondario a un cluster del servizio Azure Kubernetes (AKS) esistente.

Questo articolo presuppone una conoscenza di base dei concetti relativi a Kubernetes e Azure Load Balancer. Per altre informazioni, vedere [Concetti di base relativi a Kubernetes per il servizio Azure Kubernetes][kubernetes-concepts].

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="before-you-begin"></a>Prima di iniziare

Quando si crea un cluster per l'uso di un pool di nodi spot, il cluster deve usare anche set di scalabilità di macchine virtuali per i pool di nodi e il servizio di bilanciamento del carico dello SKU *standard* . È inoltre necessario aggiungere un pool di nodi aggiuntivo dopo aver creato il cluster per l'utilizzo di un pool di nodi spot. L'aggiunta di un pool di nodi aggiuntivo viene analizzata in un passaggio successivo.

Questo articolo richiede la versione 2,14 o successiva dell'interfaccia della riga di comando di Azure. Eseguire `az --version` per trovare la versione. Se è necessario eseguire l'installazione o l'aggiornamento, vedere [Installare l'interfaccia della riga di comando di Azure][azure-cli-install].

### <a name="limitations"></a>Limitazioni

Quando si creano e si gestiscono i cluster AKS con un pool di nodi spot, si applicano le limitazioni seguenti:

* Un pool di nodi spot non può essere il pool di nodi predefinito del cluster. Un pool di nodi spot può essere utilizzato solo per un pool secondario.
* Non è possibile aggiornare un pool di nodi spot perché i pool di nodi spot non possono garantire Cordon e svuotamento. È necessario sostituire il pool di nodi spot esistente con uno nuovo per eseguire operazioni come l'aggiornamento della versione di Kubernetes. Per sostituire un pool di nodi spot, creare un nuovo pool di nodi con una versione diversa di Kubernetes, attendere che lo stato sia *pronto* , quindi rimuovere il vecchio pool di nodi.
* Il piano di controllo e i pool di nodi non possono essere aggiornati nello stesso momento. È necessario aggiornarli separatamente oppure rimuovere il pool di nodi spot per aggiornare il piano di controllo e i pool di nodi rimanenti nello stesso momento.
* Un pool di nodi spot deve usare i set di scalabilità di macchine virtuali.
* Non è possibile modificare ScaleSetPriority o SpotMaxPrice dopo la creazione.
* Quando si imposta SpotMaxPrice, il valore deve essere-1 o un valore positivo con un massimo di cinque posizioni decimali.
* Un pool di nodi spot avrà l'etichetta *kubernetes.Azure.com/scalesetpriority:spot* , il Tain *kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* e i pod di sistema avranno l'anti-affinità.
* È necessario aggiungere una [tolleranza corrispondente][spot-toleration] per pianificare i carichi di lavoro in un pool di nodi spot.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Aggiungere un pool di nodi spot a un cluster del servizio Azure Kubernetes

È necessario aggiungere un pool di nodi spot a un cluster esistente in cui sono abilitati più pool di nodi. Altre informazioni sulla creazione di un cluster AKS con più pool di nodi sono disponibili [qui][use-multiple-node-pools].

Creare un pool di nodi usando [AZ AKS nodepool Add][az-aks-nodepool-add].
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

Per impostazione predefinita, si crea un pool di nodi con una *priorità* *regolare* nel cluster AKS quando si crea un cluster con più pool di nodi. Il comando precedente aggiunge un pool di nodi ausiliario a un cluster AKS esistente con una *priorità* di *spot* . La *priorità* di *spot* rende il pool di nodi un pool di nodi spot. Il parametro del *criterio di rimozione* è impostato su *Delete* nell'esempio precedente, che corrisponde al valore predefinito. Quando si impostano i [criteri di rimozione][eviction-policy] per l' *eliminazione* , i nodi nel set di scalabilità sottostante del pool di nodi vengono eliminati quando vengono rimossi. È anche possibile impostare i criteri di rimozione per *deallocare* . Quando si impostano i criteri di rimozione su *deallocate* , i nodi nel set di scalabilità sottostante vengono impostati sullo stato arrestato-deallocato al momento della rimozione. I nodi del conteggio dello stato arrestato-deallocato rispetto alla quota di calcolo e possono causare problemi con il ridimensionamento o l'aggiornamento del cluster. I valori dei *criteri di rimozione* e *priorità* possono essere impostati solo durante la creazione del pool di nodi. Questi valori non possono essere aggiornati in un secondo momento.

Il comando Abilita anche il [ridimensionamento][cluster-autoscaler]automatico del cluster, consigliato per l'uso con i pool di nodi spot. In base ai carichi di lavoro in esecuzione nel cluster, la scalabilità automatica del cluster aumenta e riduce il numero di nodi nel pool di nodi. Per i pool di nodi spot, il ridimensionamento automatico del cluster aumenterà il numero di nodi dopo un'eliminazione se sono ancora necessari altri nodi. Se si modifica il numero massimo di nodi che può avere un pool di nodi, è necessario modificare anche il `maxCount` valore associato al ridimensionamento automatico del cluster. Se non si usa un servizio di scalabilità automatica del cluster, al momento della rimozione, il pool di punti verrà ridotto a zero e sarà necessaria un'operazione manuale per ricevere eventuali nodi spot aggiuntivi.

> [!Important]
> Pianificare solo i carichi di lavoro nei pool di nodi spot in grado di gestire le interruzioni, ad esempio processi di elaborazione batch e ambienti di test. È consigliabile configurare i [guasti e le tolleranze][taints-tolerations] nel pool di nodi spot per garantire che solo i carichi di lavoro in grado di gestire le eliminazioni di nodi siano pianificati in un pool di nodi spot. Ad esempio, il comando precedente NY default aggiunge una macchia di *kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* in modo che in questo nodo siano pianificati solo i pod con una tolleranza corrispondente.

## <a name="verify-the-spot-node-pool"></a>Verificare il pool di nodi spot

Per verificare che il pool di nodi sia stato aggiunto come pool di nodi spot:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Confermare che *scaleSetPriority* è *spot* .

Per pianificare l'esecuzione di un pod in un nodo spot, aggiungere una tolleranza che corrisponda al valore Taint applicato al nodo spot. Nell'esempio seguente viene illustrata una parte di un file YAML che definisce una tolleranza che corrisponde a un elemento *kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* usato nel passaggio precedente.

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

Quando viene distribuito un pod con questa tolleranza, Kubernetes può pianificare correttamente il Pod nei nodi con il Taint applicato.

## <a name="max-price-for-a-spot-pool"></a>Prezzo massimo per un pool di spot
[I prezzi per le istanze di spot sono variabili][pricing-spot]in base all'area e allo SKU. Per altre informazioni, vedere prezzi per [Linux][pricing-linux] e [Windows][pricing-windows].

Con i prezzi variabili è possibile impostare un prezzo massimo, in dollari statunitensi (USD), usando al massimo 5 cifre decimali. Ad esempio, il valore *0,98765* sarà un prezzo massimo di $0,98765 USD all'ora. Se si imposta il prezzo massimo su *-1* , l'istanza non verrà rimossa in base al prezzo. Il prezzo per l'istanza sarà il prezzo corrente per spot o il prezzo di un'istanza standard, a seconda del valore minore, a condizione che siano disponibili capacità e quota.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo si è appreso come aggiungere un pool di nodi spot a un cluster AKS. Per altre informazioni su come controllare i pod nei pool di nodi, vedere [procedure consigliate per le funzionalità dell'utilità di pianificazione avanzate in AKS][operator-best-practices-advanced-scheduler].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest#az-aks-nodepool-add
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