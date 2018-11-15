---
title: Configurare funzionalità di rete avanzate nel servizio Kubernetes di Azure (AKS)
description: Informazioni su come configurare funzionalità di rete avanzate nel servizio Kubernetes di Azure (AKS), inclusa la distribuzione di un cluster AKS in una rete virtuale e una subnet esistenti.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 10/11/2018
ms.author: iainfou
ms.openlocfilehash: 289aa893a0ffa598d5b9fae67a81e9bf0c9782f7
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2018
ms.locfileid: "51014398"
---
# <a name="configure-advanced-networking-in-azure-kubernetes-service-aks"></a>Configurare funzionalità di rete avanzate nel servizio Kubernetes di Azure (AKS)

Per impostazione predefinita, i cluster AKS usano funzionalità di rete di *base*, che creano e configurano una rete virtuale e una subnet per l'uso con il cluster. Per un ulteriore controllo di queste opzioni di rete, ad esempio gli intervalli IP, è invece possibile usare le funzionalità di rete *avanzate*. Con le funzionalità di rete avanzate è possibile creare anche un cluster AKS in una rete virtuale e una subnet esistenti. Questa rete virtuale esistente spesso offre connettività a una rete locale tramite Azure ExpressRoute o VPN da sito a sito.

Questo articolo illustra come usare le funzionalità di rete avanzate per creare e usare una rete virtuale con un cluster AKS. Per altre informazioni generali sulle funzionalità di rete, vedere [Concetti relativi alla rete per le applicazioni nel servizio Kubernetes di Azure (AKS)][aks-network-concepts].

## <a name="prerequisites"></a>Prerequisiti

* La rete virtuale per il cluster AKS deve consentire la connettività Internet in uscita.
* Non creare più di un cluster AKS nella stessa subnet.
* I cluster AKS non possono usare `169.254.0.0/16`, `172.30.0.0/16` o `172.31.0.0/16` per l'intervallo di indirizzi del servizio Kubernetes.
* L'entità servizio usata dal cluster AKS deve avere almeno autorizzazioni di [Collaboratore di rete](../role-based-access-control/built-in-roles.md#network-contributor) per la subnet all'interno della rete virtuale. Se si vuole definire un [ruolo personalizzato](../role-based-access-control/custom-roles.md) invece di usare il ruolo predefinito Collaboratore di rete, sono necessarie le autorizzazioni seguenti:
  * `Microsoft.Network/virtualNetworks/subnets/join/action`
  * `Microsoft.Network/virtualNetworks/subnets/read`

## <a name="plan-ip-addressing-for-your-cluster"></a>Pianificare l'indirizzamento IP per il cluster

I cluster configurati con la rete avanzata richiedono una pianificazione aggiuntiva. Le dimensioni della rete virtuale e della subnet devono essere sufficienti sia per il numero di pod che si prevede di eseguire che per il numero di nodi per il cluster.

Gli indirizzi IP per i pod e i nodi del cluster vengono assegnati dalla subnet specificata all'interno della rete virtuale. Ogni nodo è configurato con un indirizzo IP primario. Per impostazione predefinita, l'interfaccia di rete dei contenitori di Azure preconfigura 30 indirizzi IP aggiuntivi che vengono assegnati ai pod pianificati nel nodo. Quando si aumenta il numero di istanze del cluster, ogni nodo viene configurato in modo simile con gli indirizzi IP della subnet. È anche possibile visualizzare il [numero massimo di pod per nodo](#maximum-pods-per-node).

> [!IMPORTANT]
> Per calcolare il numero di indirizzi IP necessari, è opportuno considerare le eventuali operazioni di aggiornamento e ridimensionamento. Se si imposta l'intervallo di indirizzi IP solo per supportare un numero fisso di nodi, non è possibile aggiornare o ridimensionare il cluster.
>
> - Quando si esegue l'**aggiornamento** del cluster AKS, viene distribuito un nuovo nodo nel cluster. Viene avviata l'esecuzione di servizi e carichi di lavoro nel nuovo nodo e il nodo precedente viene rimosso dal cluster. Questo processo di aggiornamento in sequenza richiede la disponibilità di almeno un altro blocco di indirizzi IP. Il numero di nodi risulta quindi pari a `n + 1`.
>
> - Quando si esegue il **ridimensionamento** di un cluster AKS, viene distribuito un nuovo nodo nel cluster. Viene avviata l'esecuzione di servizi e carichi di lavoro nel nuovo nodo. Per calcolare l'intervallo di indirizzi IP, è necessario considerare il modo in cui si vuole aumentare il numero di nodi e pod supportati dal cluster. È necessario includere anche un nodo aggiuntivo per le operazioni di aggiornamento. Il numero di nodi risulta quindi pari a `n + number-of-additional-scaled-nodes-you-anticipate + 1`.

Se si prevede che i nodi eseguano il numero massimo di pod e che distruggano e distribuiscano regolarmente pod, è necessario considerare alcuni indirizzi IP aggiuntivi per ogni nodo. L'uso di questi indirizzi implica che possano essere necessari alcuni secondi per eliminare un servizio e per consentire la distribuzione dell'indirizzo IP rilasciato per un nuovo servizio e l'acquisizione dei dati necessari.

Il piano di indirizzo IP per un cluster AKS è costituito da una rete virtuale, almeno una subnet per i nodi e i pod e un intervallo di indirizzi per il servizio Kubernetes.

| Intervallo di indirizzi / Risorsa di Azure | Limiti e dimensioni |
| --------- | ------------- |
| Rete virtuale | La rete virtuale di Azure può avere dimensioni pari a /8, ma è limitata a 65.536 indirizzi IP configurati. |
| Subnet | Deve essere sufficientemente grande da contenere i nodi, i pod e tutte le risorse Kubernetes e Azure che potrebbero essere sottoposte a provisioning nel cluster. Ad esempio, se si distribuisce un Azure Load Balancer interno, i relativi indirizzi IP front-end vengono allocati dalla subnet del cluster, ma non gli indirizzi IP pubblici. Per calcolare le dimensioni della subnet, è opportuno considerare anche eventuali operazioni di aggiornamento o le esigenze di ridimensionamento future.<p />Per calcolare le dimensioni *minime* della subnet incluso un nodo aggiuntivo per eventuali operazioni di aggiornamento: `(number of nodes + 1) + ((number of nodes + 1) * maximum pods per node that you configure)`<p/>Esempio relativo a un cluster a 50 nodi: `(51) + (51  * 30 (default)) = 1,581` (/21 o più grande)<p/>Esempio relativo a un cluster a 50 nodi che include anche il provisioning per l'aggiunta di altri 10 nodi: `(61) + (61 * 30 (default)) = 2,440` (/ 20 o superiore)<p>Se non si specifica un numero massimo di pod per nodo al momento della creazione del cluster, l'impostazione predefinita è *30*. Il numero minimo di indirizzi IP richiesti si basa su questo valore. Se si calcolano i requisiti di indirizzi IP minimi in base a un valore massimo diverso, vedere la sezione relativa a [come configurare il numero massimo di pod per nodo](#configure-maximum---new-clusters) per impostare questo valore quando si distribuisce il cluster. |
| Intervallo di indirizzi del servizio Kubernetes | Questo intervallo non deve essere usato da nessun elemento della rete che si trova su questa rete virtuale o è connesso a essa. Il CIDR dell'indirizzo del servizio deve essere più piccolo di /12. |
| Indirizzo IP del servizio DNS Kubernetes | Indirizzo IP compreso nell'intervallo di indirizzi del servizio Kubernetes che verrà usato dall'individuazione del servizio cluster (kube-dns). |
| Indirizzo del bridge Docker | Indirizzo IP (in notazione CIDR) utilizzato come indirizzo IP del bridge Docker nei nodi. Il valore predefinito è 172.17.0.1/16. |

## <a name="maximum-pods-per-node"></a>Numero massimo di pod per nodo

Il numero massimo di pod per nodo in un cluster AKS è 110. Il numero massimo *predefinito* di pod per nodo varia tra la rete di base e avanzata e il metodo di distribuzione del cluster.

| Metodo di distribuzione | Predefiniti per la rete di base | Predefiniti per la rete avanzata | Configurabile in fase di distribuzione |
| -- | :--: | :--: | -- |
| Interfaccia della riga di comando di Azure | 110 | 30 | Sì (fino a 110) |
| Modello di Resource Manager | 110 | 30 | Sì (fino a 110) |
| Portale | 110 | 30 | No  |

### <a name="configure-maximum---new-clusters"></a>Configurare il valore massimo - nuovi cluster

È possibile configurare il numero massimo di pod per nodo *solo in fase di distribuzione del cluster*. Se si esegue la distribuzione con l'interfaccia della riga di comando di Azure o con un modello di Resource Manager, è possibile impostare il numero massimo di pod per nodo su 110.

* **Azure CLI**: specificare l'`--max-pods`argomento quando si distribuisce un cluster con il comando [az aks create] [ az-aks-create]. Il valore massimo è 110.
* **Modello di Gestione risorse**: specificare la `maxPods` proprietà nell'oggetto [ManagedClusterAgentPoolProfile] quando si distribuisce un cluster con un modello di Gestione risorse. Il valore massimo è 110.
* **Portale di Azure**: non è possibile modificare il numero massimo di pod per nodo quando si distribuisce un cluster con il portale di Azure. I cluster con funzionalità di rete avanzate sono limitati a 30 pod per ogni nodo quando di esegue la distribuzione con il portale di Azure.

### <a name="configure-maximum---existing-clusters"></a>Configurare il valore massimo - cluster esistenti

Non è possibile modificare il numero massimo di pod per ogni nodo in un cluster AKS esistente. È possibile modificare il numero solo quando si distribuisce inizialmente il cluster.

## <a name="deployment-parameters"></a>Parametri di distribuzione

Quando si crea un cluster AKS, i parametri seguenti sono configurabili per la rete avanzata:

**Rete virtuale**: rete virtuale in cui si vuole distribuire il cluster Kubernetes. Per creare una nuova rete virtuale per il cluster, selezionare *Crea nuova* e seguire i passaggi della sezione *Creare una rete virtuale*. Per altre informazioni su limiti e quote per una rete virtuale di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md#azure-resource-manager-virtual-networking-limits).

**Subnet**: subnet nella rete virtuale in cui si vuole distribuire il cluster. Per creare una nuova subnet nella rete virtuale per il cluster, selezionare *Crea nuova* e seguire i passaggi della sezione *Creare una subnet*. Per la connettività ibrida, l'intervallo di indirizzi non deve sovrapporsi ad altre reti virtuali dell'ambiente in uso.

**Intervallo di indirizzi del servizio Kubernetes**: set di indirizzi IP virtuali che Kubernetes assegna a [servizi][services] nel cluster. È possibile usare qualsiasi intervallo di indirizzi privati che soddisfi i requisiti seguenti:

* Non deve essere compreso nell'intervallo di indirizzi IP della rete virtuale del cluster
* Non deve sovrapporsi ad altre reti virtuali con cui la rete virtuale del cluster effettua il peering
* Non deve sovrapporsi ad altri IP locali
* Non deve essere compreso negli intervalli `169.254.0.0/16`, `172.30.0.0/16` o `172.31.0.0/16`

Sebbene sia tecnicamente possibile specificare un intervallo di indirizzi del servizio all'interno della stessa rete virtuale del cluster, tale operazione non è consigliata. Se vengono usati intervalli IP che si sovrappongono, si può verificare un comportamento imprevedibile. Per altre informazioni, vedere la sezione [Domande frequenti](#frequently-asked-questions) di questo articolo. Per altre informazioni sui servizi Kubernetes, vedere [Services][services] (Servizi) nella documentazione di Kubernetes.

**Kubernetes DNS service IP address** (Indirizzo IP del servizio DNS Kubernetes): indirizzo IP per il servizio DNS del cluster. Questo indirizzo deve essere compreso nell'*intervallo di indirizzi del servizio Kubernetes*.

**Docker Bridge address** (Indirizzo del bridge Docker): indirizzo IP e netmask da assegnare al bridge Docker. Questo indirizzo IP non deve essere compreso nell'intervallo di indirizzi IP della rete virtuale del cluster.

## <a name="configure-networking---cli"></a>Configurare le impostazioni di rete - interfaccia della riga di comando

Quando si crea un cluster AKS con l'interfaccia della riga di comando di Azure, è anche possibile configurare funzionalità di rete avanzate. Usare i comandi seguenti per creare un nuovo cluster AKS con avanzate funzionalità di rete abilitate.

Ottenere prima di tutto l'ID risorsa della subnet esistente in cui verrà aggiunto il cluster AKS:

```console
$ az network vnet subnet list --resource-group myVnet --vnet-name myVnet --query [].id --output tsv

/subscriptions/d5b9d4b7-6fc1-46c5-bafe-38effaed19b2/resourceGroups/myVnet/providers/Microsoft.Network/virtualNetworks/myVnet/subnets/default
```

Usare il comando [az aks create][az-aks-create] con l'argomento `--network-plugin azure` per creare un cluster con funzionalità di rete avanzate. Aggiornare il valore `--vnet-subnet-id` con l'ID della subnet raccolto nel passaggio precedente:

```azurecli
az aks create --resource-group myAKSCluster --name myAKSCluster --network-plugin azure --vnet-subnet-id <subnet-id> --docker-bridge-address 172.17.0.1/16 --dns-service-ip 10.2.0.10 --service-cidr 10.2.0.0/24
```

## <a name="configure-networking---portal"></a>Configurare le impostazioni di rete - portale

Lo screenshot seguente dal portale di Azure illustra un esempio di configurazione di queste impostazioni durante la creazione del cluster AKS:

![Configurazione della rete avanzata nel portale di Azure][portal-01-networking-advanced]

## <a name="frequently-asked-questions"></a>Domande frequenti

Le domande e le risposte seguenti si applicano alla configurazione della rete**avanzata**.

* *È possibile distribuire le VM nella subnet del cluster?*

  No. La distribuzione di macchine virtuali nella subnet usata per il cluster Kubernetes non è supportata. Le macchine virtuali possono essere distribuite nella stessa rete virtuale, ma in una subnet diversa.

* *È possibile configurare criteri di rete per pod?*

  No. I criteri di rete per pod non sono attualmente supportati.

* *Il numero massimo di pod distribuibili in un nodo è configurabile?*

  Sì, quando si distribuisce un cluster con l'interfaccia della riga di comando di Azure o un modello di Gestione risorse. Consultare [Numero massimo di pod per nodo](#maximum-pods-per-node).

  Non è possibile modificare il numero massimo di pod per ogni nodo in un cluster esistente.

* *Come si configurano altre proprietà per la subnet creata durante la creazione del cluster AKS? Ad esempio, gli endpoint di servizio.*

  L'elenco completo delle proprietà per la rete virtuale e le subnet create durante la creazione del cluster AKS può essere configurato nella pagina di configurazione della rete virtuale standard nel portale di Azure.

* *È possibile usare una subnet diversa all'interno della rete virtuale del cluster per l'***intervallo di indirizzi del servizio Kubernetes**?

  Non è consigliabile, ma questa configurazione è possibile. L'intervallo di indirizzi del servizio è un set di indirizzi IP virtuali che Kubernetes assegna ai servizi nel cluster. La rete di Azure non ha visibilità sull'intervallo di indirizzi IP dei servizi del cluster Kubernetes. A causa di tale mancanza di visibilità, è possibile creare in un secondo momento nella rete virtuale del cluster una nuova subnet sovrapposta all'intervallo di indirizzi del servizio. Se si verifica una sovrapposizione di questo tipo, Kubernetes può assegnare a un servizio un indirizzo IP già usato da un'altra risorsa nella subnet, causando un comportamento imprevedibile o errori. Assicurandosi di usare un intervallo di indirizzi esterno alla rete virtuale del cluster, è possibile evitare il rischio di sovrapposizioni.

## <a name="next-steps"></a>Passaggi successivi

### <a name="networking-in-aks"></a>Rete in AKS

Per altre informazioni sulla rete in AKS, vedere gli articoli seguenti:

- [Usare un indirizzo IP statico con il bilanciamento del carico di Azure Kubernetes Service (AKS)](static-ip.md)
- [Usare un servizio di bilanciamento del carico interno con il servizio contenitore di Azure (AKS)](internal-lb.md)

- [Creare un controller di ingresso di base con connettività di rete esterna][aks-ingress-basic]
- [Abilitare il componente aggiuntivo di routing dell'applicazione HTTP][aks-http-app-routing]
- [Creare un controller di ingresso che usa una rete privata interna e l'indirizzo IP][aks-ingress-internal]
- [Creare un controller di ingresso con un indirizzo IP pubblico dinamico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-tls]
- [Creare un controller di ingresso con un indirizzo IP pubblico statico e configurare Let's Encrypt per generare automaticamente certificati TLS][aks-ingress-static-tls]

### <a name="acs-engine"></a>ACS Engine

[Azure Container Service Engine (ACS Engine)][acs-engine] è un progetto open source che genera modelli di Azure Resource Manager che è possibile usare per la distribuzione di cluster abilitati per Docker in Azure. Con ACS Engine si possono distribuire agenti di orchestrazione Kubernetes, DC/OS, in modalità Swarm e Swarm.

I cluster Kubernetes creati con ACS Engine supportano entrambi i plug-in [kubenet][kubenet] e [Azure CNI][cni-networking]. Di conseguenza, sono supportati da ACS Engine scenari di rete sia di base che avanzata.

<!-- IMAGES -->
[advanced-networking-diagram-01]: ./media/networking-overview/advanced-networking-diagram-01.png
[portal-01-networking-advanced]: ./media/networking-overview/portal-01-networking-advanced.png

<!-- LINKS - External -->
[acs-engine]: https://github.com/Azure/acs-engine
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
