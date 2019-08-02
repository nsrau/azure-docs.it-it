---
title: Configurare funzionalità di rete di Azure CNI nel servizio Azure Kubernetes
description: Informazioni su come configurare le funzionalità di rete avanzate di Azure CNI nel servizio Azure Kubernetes, inclusa la distribuzione di un cluster del servizio Azure Kubernetes in una rete e in una subnet virtuali esistenti.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 06/03/2019
ms.author: mlearned
ms.openlocfilehash: 1cc2849ffe55fff737993140a1d0f18182820eff
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68498576"
---
# <a name="configure-azure-cni-networking-in-azure-kubernetes-service-aks"></a>Configurare funzionalità di rete di Azure CNI nel servizio Azure Kubernetes

Per impostazione predefinita, i cluster AKS usano [kubenet][kubenet]e vengono create una rete virtuale e una subnet. Con *kubenet* i nodi ottengono un indirizzo IP da una subnet della rete virtuale. Il protocollo NAT (Network Address Translation) viene quindi configurato nei nodi e i pod ricevono un indirizzo IP "nascosto" dietro l'indirizzo IP del nodo. Questo approccio riduce il numero di indirizzi IP che è necessario riservare ai pod nello spazio degli indirizzi della rete.

Con l' [interfaccia di rete del contenitore di Azure (CNI)][cni-networking], ogni pod ottiene un indirizzo IP dalla subnet ed è possibile accedervi direttamente. Questi indirizzi IP devono essere univoci nello spazio di indirizzi della rete e devono essere pianificati in anticipo. Ogni nodo ha un parametro di configurazione per il numero massimo di pod che supporta. Il numero equivalente di indirizzi IP per nodo viene quindi riservato anticipatamente per tale nodo. Questo approccio richiede una maggiore pianificazione e spesso conduce all'esaurimento degli indirizzi IP o alla necessità di ricompilare i cluster in una subnet di dimensioni maggiori man mano che le esigenze dell'applicazione aumentano.

Questo articolo illustra come usare le funzionalità di rete di *Azure CNI* per creare e usare una subnet di rete virtuale per un cluster del servizio Azure Kubernetes. Per altre informazioni sulle opzioni di rete e le considerazioni, vedere [concetti di rete per Kubernetes e AKS][aks-network-concepts].

## <a name="prerequisites"></a>Prerequisiti

* La rete virtuale per il cluster servizio Azure Kubernetes deve consentire la connettività Internet in uscita.
* Non creare più di un cluster servizio Azure Kubernetes nella stessa subnet.
* I cluster AKS non possono usare `169.254.0.0/16`, `172.30.0.0/16`, `172.31.0.0/16`o `192.0.2.0/24` per l'intervallo di indirizzi del servizio Kubernetes.
* L'entità servizio usata dal cluster servizio Azure Kubernetes deve avere almeno autorizzazioni di [Collaboratore di rete](../role-based-access-control/built-in-roles.md#network-contributor) per la subnet all'interno della rete virtuale. Se si vuole definire un [ruolo personalizzato](../role-based-access-control/custom-roles.md) invece di usare il ruolo predefinito Collaboratore di rete, sono necessarie le autorizzazioni seguenti:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>Pianificare l'indirizzamento IP per il cluster

I cluster configurati con Azure CNI richiedono una pianificazione aggiuntiva. Le dimensioni della rete virtuale e della subnet devono essere sufficienti sia per il numero di pod che si prevede di eseguire che per il numero di nodi per il cluster.

Gli indirizzi IP per i pod e i nodi del cluster vengono assegnati dalla subnet specificata all'interno della rete virtuale. Ogni nodo è configurato con un indirizzo IP primario. Per impostazione predefinita, l'interfaccia di rete dei contenitori di Azure preconfigura 30 indirizzi IP aggiuntivi che vengono assegnati ai pod pianificati nel nodo. Quando si aumenta il numero di istanze del cluster, ogni nodo viene configurato in modo simile con gli indirizzi IP della subnet. È anche possibile visualizzare il [numero massimo di pod per nodo](#maximum-pods-per-node).

> [!IMPORTANT]
> Per calcolare il numero di indirizzi IP necessari, è opportuno considerare le eventuali operazioni di aggiornamento e ridimensionamento. Se si imposta l'intervallo di indirizzi IP solo per supportare un numero fisso di nodi, non è possibile aggiornare o ridimensionare il cluster.
>
> - Quando si esegue l'**aggiornamento** del cluster servizio Azure Kubernetes, viene distribuito un nuovo nodo nel cluster. Viene avviata l'esecuzione di servizi e carichi di lavoro nel nuovo nodo e il nodo precedente viene rimosso dal cluster. Questo processo di aggiornamento in sequenza richiede la disponibilità di almeno un altro blocco di indirizzi IP. Il numero di nodi risulta quindi pari a `n + 1`.
>   - Questa considerazione è particolarmente importante quando si usano i pool di nodi di Windows Server (attualmente in anteprima in AKS). I nodi di Windows Server in AKS non applicano automaticamente gli aggiornamenti di Windows, ma si esegue un aggiornamento nel pool di nodi. Questo aggiornamento distribuisce nuovi nodi con l'immagine del nodo di base di Windows Server 2019 e le patch di sicurezza più recenti. Per ulteriori informazioni sull'aggiornamento di un pool di nodi di Windows Server, vedere [aggiornare un pool di nodi in AKS][nodepool-upgrade].
>
> - Quando si esegue il **ridimensionamento** di un cluster servizio Azure Kubernetes, viene distribuito un nuovo nodo nel cluster. Viene avviata l'esecuzione di servizi e carichi di lavoro nel nuovo nodo. Per calcolare l'intervallo di indirizzi IP, è necessario considerare il modo in cui si vuole aumentare il numero di nodi e pod supportati dal cluster. È necessario includere anche un nodo aggiuntivo per le operazioni di aggiornamento. Il numero di nodi risulta quindi pari a `n + number-of-additional-scaled-nodes-you-anticipate + 1`.

Se si prevede che i nodi eseguano il numero massimo di pod e che distruggano e distribuiscano regolarmente pod, è necessario considerare alcuni indirizzi IP aggiuntivi per ogni nodo. L'uso di questi indirizzi implica che possano essere necessari alcuni secondi per eliminare un servizio e per consentire la distribuzione dell'indirizzo IP rilasciato per un nuovo servizio e l'acquisizione dei dati necessari.

Il piano di indirizzo IP per un cluster servizio Azure Kubernetes è costituito da una rete virtuale, almeno una subnet per i nodi e i pod e un intervallo di indirizzi per il servizio Kubernetes.

| Intervallo di indirizzi / Risorsa di Azure | Limiti e dimensioni |
| --------- | ------------- |
| Rete virtuale | La rete virtuale di Azure può avere dimensioni pari a /8, ma è limitata a 65.536 indirizzi IP configurati. |
| Subnet | Deve essere sufficientemente grande da contenere i nodi, i pod e tutte le risorse Kubernetes e Azure che potrebbero essere sottoposte a provisioning nel cluster. Ad esempio, se si distribuisce un Azure Load Balancer interno, i relativi indirizzi IP front-end vengono allocati dalla subnet del cluster, ma non gli indirizzi IP pubblici. Per calcolare le dimensioni della subnet, è opportuno considerare anche eventuali operazioni di aggiornamento o le esigenze di ridimensionamento future.<p />Per calcolare le dimensioni *minime* della subnet incluso un nodo aggiuntivo per eventuali operazioni di aggiornamento: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Esempio relativo a un cluster a 50 nodi: `(51) + (51  * 30 (default)) = 1,581` (/21 o più grande)<p/>Esempio relativo a un cluster a 50 nodi che include anche il provisioning per l'aggiunta di altri 10 nodi: `(61) + (61 * 30 (default)) = 1,891` (/21 o superiore)<p>Se non si specifica un numero massimo di pod per nodo al momento della creazione del cluster, l'impostazione predefinita è *30*. Il numero minimo di indirizzi IP richiesti si basa su questo valore. Se si calcolano i requisiti di indirizzi IP minimi in base a un valore massimo diverso, vedere la sezione relativa a [come configurare il numero massimo di pod per nodo](#configure-maximum---new-clusters) per impostare questo valore quando si distribuisce il cluster. |
| Intervallo degli indirizzi del servizio Kubernetes | Questo intervallo non deve essere usato da nessun elemento della rete che si trova su questa rete virtuale o è connesso a essa. Il CIDR dell'indirizzo del servizio deve essere più piccolo di /12. |
| Indirizzo IP del servizio DNS Kubernetes | Indirizzo IP compreso nell'intervallo di indirizzi del servizio Kubernetes che verrà usato dall'individuazione del servizio cluster (kube-dns). Non usare il primo indirizzo IP nell'intervallo di indirizzi, ad esempio .1. Il primo indirizzo nell'intervallo della subnet è usato per l'indirizzo *kubernetes.default.svc.cluster.local*. |
| Indirizzo del bridge Docker | Indirizzo IP (in notazione CIDR) utilizzato come indirizzo IP del bridge Docker nei nodi. Questo CIDR è associato al numero di contenitori nel nodo. Il valore predefinito è 172.17.0.1/16. |

## <a name="maximum-pods-per-node"></a>Numero massimo di pod per nodo

Il numero massimo di pod per nodo in un cluster AKS è 250. Il numero massimo *predefinito* di pod per nodo varia tra le funzionalità di rete *kubenet* e *Azure CNI* e il metodo di distribuzione del cluster.

| Metodo di distribuzione | Kubenet predefinito | Azure CNI predefinito | Configurabile in fase di distribuzione |
| -- | :--: | :--: | -- |
| Interfaccia della riga di comando di Azure | 110 | 30 | Sì (fino a 250) |
| Modello di Resource Manager | 110 | 30 | Sì (fino a 250) |
| Portale | 110 | 30 | No |

### <a name="configure-maximum---new-clusters"></a>Configurare il valore massimo - nuovi cluster

È possibile configurare il numero massimo di pod per nodo *solo in fase di distribuzione del cluster*. Se si esegue la distribuzione con l'interfaccia della riga di comando di Azure o con un modello di Gestione risorse, è possibile impostare il numero massimo di pod per nodo su un valore massimo di 250.

| Rete | Minima | Massima |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

> [!NOTE]
> Il valore minimo nella tabella precedente viene applicato rigorosamente dal servizio AKS. Non è possibile impostare un valore maxPods inferiore a quello minimo visualizzato in modo da impedire l'avvio del cluster.

* **Interfaccia della riga di comando di Azure**: Specificare l' `--max-pods` argomento quando si distribuisce un cluster con il comando [AZ AKS create][az-aks-create] . Il valore massimo è 250.
* **Modello di Resource Manager**: specificare la `maxPods` proprietà nell'oggetto [ManagedClusterAgentPoolProfile] quando si distribuisce un cluster con un modello di Resource Manager. Il valore massimo è 250.
* **Portale di Azure**: non è possibile modificare il numero massimo di pod per nodo quando si distribuisce un cluster con il portale di Azure. I cluster con funzionalità di rete Azure CNI sono limitati a 30 pod per ogni nodo quando si esegue la distribuzione con il portale di Azure.

### <a name="configure-maximum---existing-clusters"></a>Configurare il valore massimo - cluster esistenti

Non è possibile modificare il numero massimo di pod per ogni nodo in un cluster servizio Azure Kubernetes esistente. È possibile modificare il numero solo quando si distribuisce inizialmente il cluster.

## <a name="deployment-parameters"></a>Parametri di distribuzione

Quando si crea un cluster servizio Azure Kubernetes, per la rete Azure CNI i parametri seguenti sono configurabili:

**Rete virtuale**: rete virtuale in cui si vuole distribuire il cluster Kubernetes. Per creare una nuova rete virtuale per il cluster, selezionare *Crea nuova* e seguire i passaggi della sezione *Creare una rete virtuale*. Per altre informazioni su limiti e quote per una rete virtuale di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Subnet**: subnet nella rete virtuale in cui si vuole distribuire il cluster. Per creare una nuova subnet nella rete virtuale per il cluster, selezionare *Crea nuova* e seguire i passaggi della sezione *Creare una subnet*. Per la connettività ibrida, l'intervallo di indirizzi non deve sovrapporsi ad altre reti virtuali dell'ambiente in uso.

**Intervallo di indirizzi del servizio Kubernetes**: Questo è il set di indirizzi IP virtuali che Kubernetes assegna ai [Servizi][services] interni nel cluster. È possibile usare qualsiasi intervallo di indirizzi privati che soddisfi i requisiti seguenti:

* Non deve essere compreso nell'intervallo di indirizzi IP della rete virtuale del cluster
* Non deve sovrapporsi ad altre reti virtuali con cui la rete virtuale del cluster effettua il peering
* Non deve sovrapporsi ad altri IP locali
* Non devono essere compresi negli intervalli `169.254.0.0/16` `172.31.0.0/16`, `172.30.0.0/16`, o`192.0.2.0/24`

Sebbene sia tecnicamente possibile specificare un intervallo di indirizzi del servizio all'interno della stessa rete virtuale del cluster, tale operazione non è consigliata. Se vengono usati intervalli IP che si sovrappongono, si può verificare un comportamento imprevedibile. Per altre informazioni, vedere la sezione [Domande frequenti](#frequently-asked-questions) di questo articolo. Per ulteriori informazioni sui servizi Kubernetes, vedere [Servizi][services] nella documentazione di Kubernetes.

**Indirizzo IP del servizio DNS Kubernetes**:  L'indirizzo IP per il servizio DNS del cluster. Questo indirizzo deve essere compreso nell'*intervallo di indirizzi del servizio Kubernetes*. Non usare il primo indirizzo IP nell'intervallo di indirizzi, ad esempio .1. Il primo indirizzo nell'intervallo della subnet è usato per l'indirizzo *kubernetes.default.svc.cluster.local*.

**Indirizzo del bridge Docker**: L'indirizzo di rete del Bridge Docker rappresenta l'indirizzo di rete del Bridge *docker0* predefinito presente in tutte le installazioni di Docker. Sebbene *docker0* Bridge non venga usato dai cluster AKS o dai pod stessi, è necessario impostare questo indirizzo per continuare a supportare scenari come la *compilazione* di Docker nel cluster AKS. È necessario selezionare un CIDR per l'indirizzo di rete del Bridge Docker perché, in caso contrario, Docker selezionerà automaticamente una subnet che potrebbe essere in conflitto con altri CIDRs. È necessario selezionare uno spazio di indirizzi che non entri in conflitto con il resto del CIDRs nelle reti, tra cui la CIDR del servizio del cluster e il CIDR del Pod.

## <a name="configure-networking---cli"></a>Configurare le impostazioni di rete - interfaccia della riga di comando

Quando si crea un cluster del servizio Azure Kubernetes con l'interfaccia della riga di comando di Azure, è anche possibile configurare le funzionalità di rete di Azure CNI. Usare i comandi seguenti per creare un nuovo cluster del servizio Azure Kubernetes con funzionalità di rete di Azure CNI abilitate.

Ottenere prima di tutto l'ID risorsa della subnet esistente in cui verrà aggiunto il cluster servizio Azure Kubernetes:

```azurecli-interactive
$ az network vnet subnet list \
    --resource-group myVnet \
    --vnet-name myVnet \
    --query [].id --output tsv

/subscriptions/<guid>/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Usare il comando [AZ AKS create][az-aks-create] con l' `--network-plugin azure` argomento per creare un cluster con rete avanzata. Aggiornare il valore `--vnet-subnet-id` con l'ID della subnet raccolto nel passaggio precedente:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --generate-ssh-keys
```

## <a name="configure-networking---portal"></a>Configurare le impostazioni di rete - portale

Lo screenshot seguente dal portale di Azure illustra un esempio di configurazione di queste impostazioni durante la creazione del cluster servizio Azure Kubernetes:

![Configurazione della rete avanzata nel portale di Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Domande frequenti

Le domande e le risposte seguenti si applicano alla configurazione delle funzionalità di rete di **Azure CNI**.

* *È possibile distribuire le VM nella subnet del cluster?*

  No. La distribuzione di macchine virtuali nella subnet usata per il cluster Kubernetes non è supportata. Le macchine virtuali possono essere distribuite nella stessa rete virtuale, ma in una subnet diversa.

* *È possibile configurare criteri di rete per pod?*

  Sì, il criterio di rete Kubernetes è disponibile in AKS. Per iniziare, vedere [proteggere il traffico tra i pod usando i criteri di rete in AKS][network-policy].

* *Il numero massimo di pod distribuibili in un nodo è configurabile?*

  Sì, quando si distribuisce un cluster con l'interfaccia della riga di comando di Azure o un modello di Gestione risorse. Consultare [Numero massimo di pod per nodo](#maximum-pods-per-node).

  Non è possibile modificare il numero massimo di pod per ogni nodo in un cluster esistente.

* *Come si configurano altre proprietà per la subnet creata durante la creazione del cluster servizio Azure Kubernetes? Ad esempio, gli endpoint di servizio.*

  L'elenco completo delle proprietà per la rete virtuale e le subnet create durante la creazione del cluster servizio Azure Kubernetes può essere configurato nella pagina di configurazione della rete virtuale standard nel portale di Azure.

* *È possibile usare una subnet diversa all'interno della rete virtuale del cluster per l'* **intervallo di indirizzi del servizio Kubernetes**?

  Non è consigliabile, ma questa configurazione è possibile. L'intervallo di indirizzi del servizio è un set di indirizzi IP virtuali che Kubernetes assegna ai servizi interni nel cluster. La rete di Azure non ha visibilità sull'intervallo di indirizzi IP dei servizi del cluster Kubernetes. A causa di tale mancanza di visibilità, è possibile creare in un secondo momento nella rete virtuale del cluster una nuova subnet sovrapposta all'intervallo di indirizzi del servizio. Se si verifica una sovrapposizione di questo tipo, Kubernetes può assegnare a un servizio un indirizzo IP già usato da un'altra risorsa nella subnet, causando un comportamento imprevedibile o errori. Assicurandosi di usare un intervallo di indirizzi esterno alla rete virtuale del cluster, è possibile evitare il rischio di sovrapposizioni.

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulla rete in servizio Azure Kubernetes, vedere gli articoli seguenti:

- [Usare un indirizzo IP statico con il bilanciamento del carico del servizio Azure Kubernetes](static-ip.md)
- [Usare un servizio di bilanciamento del carico interno con il servizio Azure Container](internal-lb.md)

- [Creare un controller di ingresso di base con connettività di rete esterna][aks-ingress-basic]
- [Abilita il componente aggiuntivo routing applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usi una rete interna, privata e un indirizzo IP][aks-ingress-internal]
- [Creare un controller di ingresso con un indirizzo IP pubblico dinamico e configurare la crittografia per la generazione automatica di certificati TLS][aks-ingress-tls]
- [Creare un controller di ingresso con un IP pubblico statico e configurare la crittografia per la generazione automatica di certificati TLS][aks-ingress-static-tls]

### <a name="aks-engine"></a>Motore AKS

Il motore del [servizio Kubernetes di Azure (motore AKS)][aks-engine] è un progetto open source che genera modelli di Azure Resource Manager che è possibile usare per la distribuzione di cluster Kubernetes in Azure.

I cluster Kubernetes creati con il motore AKS supportano entrambi i plug-in [kubenet][kubenet] e [Azure CNI][cni-networking] . Di conseguenza, entrambi gli scenari di rete sono supportati dal motore del servizio Azure Kubernetes.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[aks-engine]: https://github.com/Azure/aks-engine
[services]: https://kubernetes.io/docs/concepts/services-networking/service/
[portal]: https://portal.azure.com
[cni-networking]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[kubenet]: https://kubernetes.io/docs/concepts/cluster-administration/network-plugins/#kubenet

<!-- LINKS - Internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[aks-ssh]: ssh.md
[ManagedClusterAgentPoolProfile]: /azure/templates/microsoft.containerservice/managedclusters#managedclusteragentpoolprofile-object
[aks-network-concepts]: concepts-network.md
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-internal]: ingress-internal-ip.md
[network-policy]: use-network-policies.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[network-comparisons]: concepts-network.md#compare-network-models
