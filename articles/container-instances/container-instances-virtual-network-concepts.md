---
title: Scenari di utilizzo di una rete virtuale
description: Scenari, risorse e limitazioni per la distribuzione di gruppi di contenitori in una rete virtuale di Azure.
ms.topic: article
ms.date: 04/29/2020
ms.author: danlep
ms.openlocfilehash: 77fbdb1720e571027f28b5bdca5c0e3c65c3ded2
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82584402"
---
# <a name="virtual-network-scenarios-and-resources"></a>Scenari e risorse della rete virtuale

[Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md) offre reti private e sicure per le risorse di Azure e locali. Grazie alla distribuzione di gruppi di contenitori in una rete virtuale di Azure, i contenitori possono comunicare in modo sicuro con altre risorse nella rete virtuale. 

Questo articolo fornisce informazioni di base su scenari, limitazioni e risorse della rete virtuale. Per esempi di distribuzione che usano l'interfaccia della riga di comando di Azure, vedere [distribuire istanze di contenitore in una rete virtuale di Azure](container-instances-vnet.md).

## <a name="scenarios"></a>Scenari

Gruppi di contenitori distribuiti in una rete virtuale di Azure abilitano scenari analoghi ai seguenti:

* Comunicazione diretta tra i gruppi di contenitori nella stessa subnet
* Invio dell'output di carichi di lavoro [basati su attività](container-instances-restart-policy.md) dalle istanze di contenitore a un database nella rete virtuale
* Recuperare contenuto per le istanze di contenitore da un [endpoint di servizio](../virtual-network/virtual-network-service-endpoints-overview.md) nella rete virtuale
* Abilitare la comunicazione del contenitore con le risorse locali tramite un [gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [ExpressRoute](../expressroute/expressroute-introduction.md)
* Eseguire l'integrazione con il [firewall di Azure](../firewall/overview.md) per identificare il traffico in uscita proveniente dal contenitore 
* Risolvere i nomi tramite il DNS interno di Azure per la comunicazione con le risorse di Azure nella rete virtuale, ad esempio le macchine virtuali
* Usare le regole NSG per controllare l'accesso ai contenitori a subnet o altre risorse di rete

## <a name="unsupported-networking-scenarios"></a>Scenari di rete non supportati 

* **Azure Load Balancer** l'inserimento di un Azure Load Balancer davanti alle istanze di contenitore in un gruppo di contenitori in rete non è supportato
* **Peering di rete virtuale globale** : il peering globale (connessione di reti virtuali tra aree di Azure) non è supportato
* **IP pubblico o etichetta DNS** : i gruppi di contenitori distribuiti in una rete virtuale non supportano attualmente l'esposizione di contenitori direttamente a Internet con un indirizzo IP pubblico o un nome di dominio completo

## <a name="other-limitations"></a>Altre limitazioni

* Attualmente, solo i contenitori Linux sono supportati in un gruppo di contenitori distribuito in una rete virtuale.
* Per distribuire gruppi di contenitori in una subnet, la subnet non può contenere altri tipi di risorse. Rimuovere tutte le risorse presenti in una subnet esistente prima di distribuire gruppi di contenitori nella subnet oppure creare una nuova subnet.
* Non è possibile usare un' [identità gestita](container-instances-managed-identity.md) in un gruppo di contenitori distribuito in una rete virtuale.
* Non è possibile abilitare un probe di [Livezza](container-instances-liveness-probe.md) o un probe di [conformità](container-instances-readiness-probe.md) in un gruppo di contenitori distribuito in una rete virtuale.
* A causa delle risorse di rete aggiuntive, le distribuzioni in una rete virtuale sono in genere più lente rispetto alla distribuzione di un'istanza di contenitore standard.

## <a name="where-to-deploy"></a>Dove eseguire la distribuzione

Le aree e le risorse massime seguenti sono disponibili per distribuire un gruppo di contenitori in una rete virtuale di Azure.

[!INCLUDE [container-instances-vnet-limits](../../includes/container-instances-vnet-limits.md)]

## <a name="required-network-resources"></a>Risorse di rete necessarie

Per distribuire gruppi di contenitori in una rete virtuale, sono necessarie tre risorse di Rete virtuale di Azure, ovvero la [rete virtuale](#virtual-network) stessa, una [subnet delegata](#subnet-delegated) nella rete virtuale e un [profilo di rete](#network-profile). 

### <a name="virtual-network"></a>Rete virtuale

Una rete virtuale definisce lo spazio degli indirizzi in cui si creano una o più subnet. A questo punto è possibile distribuire le risorse di Azure (ad esempio i gruppi di contenitori) nelle subnet nella rete virtuale.

### <a name="subnet-delegated"></a>Subnet (delegata)

Le subnet segmentano la rete virtuale in spazi di indirizzi separati usabili dalle risorse di Azure contenute. In una rete virtuale possono essere create una o più subnet.

La subnet usata per i gruppi di contenitori può includere solo gruppi di contenitori. Quando si distribuisce un gruppo di contenitori in una subnet, Azure delega tale subnet a Istanze di Azure Container. Dopo che è stata delegata, la subnet può essere usata solo per i gruppi di contenitori. Se si prova a distribuire risorse diverse da gruppi di contenitori in una subnet delegata, l'operazione ha esito negativo.

### <a name="network-profile"></a>Profilo di rete

Un profilo di rete è un modello di configurazione di rete per le risorse di Azure. Il profilo specifica determinate proprietà di rete per la risorsa, ad esempio la subnet in cui deve essere distribuito. Quando si usa per la prima volta il comando [az container create][az-container-create] per distribuire un gruppo di contenitori in una subnet (e quindi una rete virtuale), Azure crea automaticamente un profilo di rete. È quindi possibile usare tale profilo di rete per le distribuzioni future nella subnet. 

Per usare un modello di Resource Manager, il file YAML o un metodo a livello di codice per distribuire un gruppo di contenitori in una subnet, è necessario specificare l'ID risorsa di Resource Manager completo di un profilo di rete. È possibile usare un profilo creato in precedenza usando [az container create][az-container-create] oppure creare un profilo usando un modello di Resource Manager (vedere l'[esempio di modello](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet) e le [informazioni di riferimento](https://docs.microsoft.com/azure/templates/microsoft.network/networkprofiles)). Per ottenere l'ID di un profilo creato in precedenza, usare il comando [az network profile list][az-network-profile-list]. 

Nel diagramma seguente diversi gruppi di contenitori sono stati distribuiti in una subnet delegata a Istanze di Azure Container. Dopo aver distribuito un gruppo di contenitori in una subnet, è possibile distribuire gruppi di contenitori aggiuntivi ala subnet specificando lo stesso profilo di rete.

![Gruppi di contenitori in una rete virtuale][aci-vnet-01]

## <a name="next-steps"></a>Passaggi successivi

* Per esempi di distribuzione con l'interfaccia della riga di comando di Azure, vedere [distribuire istanze di contenitore in una rete virtuale di Azure](container-instances-vnet.md).
* Per distribuire una nuova rete virtuale, subnet, profilo di rete e gruppo di contenitori usando un modello di Resource Manager, vedere [Create an Azure container group with VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-aci-vnet
) (Creare un gruppo di contenitori di Azure con VNet).


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-virtual-network-concepts/aci-vnet-01.png

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-network-profile-list]: /cli/azure/network/profile#az-network-profile-list

