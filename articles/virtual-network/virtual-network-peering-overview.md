---
title: Peering di rete virtuale di Azure
titlesuffix: Azure Virtual Network
description: Informazioni sul peering reti virtuali in Azure.
services: virtual-network
documentationcenter: na
author: anavinahar
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2019
ms.author: anavin
ms.openlocfilehash: 59854d7d46f533510bea97a6845554fc0ce83dbb
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74328357"
---
# <a name="virtual-network-peering"></a>Peering di rete virtuale

Il peering di rete virtuale consente di connettere facilmente le reti nella [rete virtuale di Azure](virtual-networks-overview.md). Le reti virtuali vengono visualizzate come una a scopo di connettività. Il traffico tra macchine virtuali usa l'infrastruttura backbone di Microsoft. Come il traffico tra macchine virtuali nella stessa rete, il traffico viene instradato solo attraverso la rete *privata* di Microsoft.

Azure supporta i tipi di peering seguenti:

* Peering di rete virtuale: connettere reti virtuali all'interno della stessa area di Azure.
* Peering di rete virtuale globale: connessione di reti virtuali tra aree di Azure.

Il peering reti virtuali, locale o globale, include i vantaggi seguenti:

* Connessione a bassa latenza e larghezza di banda elevata tra le risorse in reti virtuali diverse.
* Possibilità per le risorse in una rete virtuale di comunicare con le risorse in una rete virtuale diversa.
* La possibilità di trasferire dati tra reti virtuali tra sottoscrizioni di Azure, Azure Active Directory tenant, modelli di distribuzione e aree di Azure.
* Possibilità di eseguire il peering di reti virtuali create tramite il Azure Resource Manager.
* La possibilità di eseguire il peering di una rete virtuale creata tramite Gestione risorse a un'unica creazione tramite il modello di distribuzione classica. Per altre informazioni in proposito, vedere le [informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/resource-manager-deployment-model.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Tempo di inattività nullo per le risorse nelle due reti virtuali durante o dopo la creazione del peering.

Il traffico di rete tra reti virtuali di cui è stato eseguito il peering è privato. Il traffico tra le reti virtuali viene mantenuto nella rete backbone Microsoft. Nella comunicazione tra le reti virtuali non sono necessari gateway, una rete Internet pubblica o la crittografia.

## <a name="connectivity"></a>Connettività

Per le reti virtuali con peering, le risorse in una delle due reti virtuali possono connettersi direttamente alle risorse nella rete virtuale con peering.

La latenza di rete tra macchine virtuali presenti in reti virtuali di cui è stato eseguito il peering nella stessa area è identica a quella in un'unica rete virtuale. La velocità effettiva della rete si basa sulla larghezza di banda consentita per la macchina virtuale proporzionalmente alle relative dimensioni, senza altre restrizioni alla larghezza di banda consentita nel peering.

Il traffico tra macchine virtuali presenti in reti virtuali di cui è stato eseguito il peering viene direttamente instradato attraverso l'infrastruttura backbone Microsoft, non attraverso un gateway né tramite la rete Internet pubblica.

È possibile applicare gruppi di sicurezza di rete in entrambe le reti virtuali per bloccare l'accesso ad altre reti o subnet virtuali.
Quando si configura il peering di rete virtuale, aprire o chiudere le regole del gruppo di sicurezza di rete tra le reti virtuali. Se si apre la connettività completa tra reti virtuali con peering, è possibile applicare gruppi di sicurezza di rete per bloccare o negare un accesso specifico. La connettività completa è l'opzione predefinita. Per altre informazioni sui gruppi di sicurezza di rete, vedere [gruppi di sicurezza](security-overview.md).

## <a name="service-chaining"></a>Concatenamento dei servizi

Il concatenamento dei servizi consente di indirizzare il traffico da una rete virtuale a un'appliance virtuale o a un gateway in una rete con peering tramite route definite dall'utente.

Per abilitare il concatenamento dei servizi, configurare route definite dall'utente che puntano a macchine virtuali in reti virtuali con peering come indirizzo IP *hop successivo* . Le route definite dall'utente possono anche puntare ai gateway di rete virtuale per abilitare il concatenamento del servizio.

È possibile distribuire reti hub e spoke, in cui la rete virtuale dell'hub ospita i componenti dell'infrastruttura, ad esempio un'appliance virtuale di rete o un gateway VPN. e tutte le reti virtuali spoke possono eseguire il peering con la rete virtuale dell'hub. Il traffico fluisce attraverso le appliance virtuali di rete o i gateway VPN nella rete virtuale dell'hub.

Il peering di rete virtuale consente di usare come hop successivo di una route definita dall'utente l'indirizzo IP di una macchina virtuale nella rete virtuale con peering oppure un gateway VPN. Non è possibile indirizzare tra reti virtuali con una route definita dall'utente che specifica un gateway Azure ExpressRoute come tipo di hop successivo. Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente](virtual-networks-udr-overview.md#user-defined). Per informazioni su come creare una topologia di rete hub-spoke, vedere [topologia di rete hub-spoke in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Gateway e connettività locale

Ogni rete virtuale, inclusa una rete virtuale con peering, può avere un proprio gateway. Una rete virtuale può usare il gateway per connettersi a una rete locale. È anche possibile configurare [connessioni da rete virtuale a rete virtuale](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) tramite gateway, anche per le reti virtuali con peering.

Quando si configurano entrambe le opzioni per l'interconnettività della rete virtuale, il traffico tra le reti virtuali passa attraverso la configurazione del peering. Il traffico USA il backbone di Azure.

È anche possibile configurare il gateway nella rete virtuale con peering come punto di transito a una rete locale. In questo caso, la rete virtuale che usa un gateway remoto non può avere un proprio gateway. Una rete virtuale ha un solo gateway. Il gateway è un gateway locale o remoto nella rete virtuale con peering, come illustrato nel diagramma seguente:

![Peering reti virtuali con transito](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Il peering di rete virtuale e il peering di rete virtuale globale supportano il transito del gateway.

Il transito del gateway tra le reti virtuali create tramite modelli di distribuzione diversi è supportato. Il gateway deve trovarsi nella rete virtuale nel modello di Gestione risorse. Per altre informazioni sull'uso di un gateway per la trasmissione, vedere [Configurare un gateway VPN per il transito nel peering di rete virtuale](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Quando si esegue il peering di reti virtuali che condividono una singola connessione Azure ExpressRoute, il traffico tra di essi passa attraverso la relazione di peering. Il traffico USA la rete backbone di Azure. È comunque possibile continuare a usare i gateway locali in ogni rete virtuale per connettersi al circuito locale. In caso contrario, è possibile usare un gateway condiviso e configurare il transito per la connettività locale.

## <a name="troubleshoot"></a>Risolvere i problemi

Per verificare che sia stato utilizzato il peering delle reti virtuali, è possibile verificare le route valide. Controllare le route per un'interfaccia di rete in qualsiasi subnet in una rete virtuale. Se esiste un peering di rete virtuale, tutte le subnet all'interno della rete virtuale hanno route con tipo di hop successivo *Peering reti virtuali* per ogni spazio degli indirizzi di ogni rete virtuale con peering. Per ulteriori informazioni, vedere [diagnosticare un problema di routing di una macchina virtuale](diagnose-network-routing-problem.md).

È anche possibile risolvere i problemi di connettività a una macchina virtuale in una rete virtuale con peering usando Azure Network Watcher. Un controllo della connettività consente di vedere come il traffico viene instradato dall'interfaccia di rete di una macchina virtuale di origine all'interfaccia di rete di una macchina virtuale di destinazione. Per altre informazioni, vedere [risolvere i problemi relativi alle connessioni con Azure Network Watcher usando il portale di Azure](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine).

È anche possibile provare a [risolvere i problemi relativi al peering di rete virtuale](virtual-network-troubleshoot-peering-issues.md).

## Vincoli per le reti virtuali con peering<a name="requirements-and-constraints"></a>

I vincoli seguenti si applicano solo quando il peering viene eseguito globalmente nelle reti virtuali:

* Le risorse in una rete virtuale non possono comunicare con l'indirizzo IP front-end di un Load Balancer interno di base (ILB) in una rete virtuale con peering globale.
* Alcuni servizi che usano un servizio di bilanciamento del carico di base non funzionano sul peering di rete virtuale globale. Per altre informazioni, vedere [quali sono i vincoli correlati al peering VNet globale e ai bilanciamenti del carico?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

Per ulteriori informazioni, vedere [requisiti e vincoli](virtual-network-manage-peering.md#requirements-and-constraints). Per altre informazioni sul numero supportato di peering, vedere [limiti di rete](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>autorizzazioni

Per informazioni sulle autorizzazioni necessarie per creare un peering di rete virtuale, vedere [autorizzazioni](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Prezzi

È previsto un addebito nominale per il traffico in ingresso e in uscita che usa una connessione di peering di rete virtuale. Per altre informazioni, vedere [prezzi di rete virtuale](https://azure.microsoft.com/pricing/details/virtual-network).

Il transito del gateway è una proprietà di peering che consente a una rete virtuale di usare un gateway VPN/ExpressRoute in una rete virtuale con peering. Il transito del gateway funziona sia per la connettività tra più sedi locali che per quella di rete. Il traffico verso il gateway (in ingresso o in uscita) nella rete virtuale con peering comporta addebiti per il peering di rete virtuale. Per altre informazioni, vedere [prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway/) del gateway VPN per i costi del gateway VPN e prezzi del gateway ExpressRoute per i costi del gateway ExpressRoute.

>[!NOTE]
> Una versione precedente di questo documento ha indicato che gli addebiti per il peering di rete virtuale non si applicano al transito del gateway. Ora riflette i prezzi accurati per la pagina dei prezzi.

## <a name="next-steps"></a>Passaggi successivi

* È possibile creare un peering tra due reti virtuali. Le reti possono appartenere alla stessa sottoscrizione, a modelli di distribuzione diversi nella stessa sottoscrizione o a sottoscrizioni diverse. Completare un'esercitazione per uno degli scenari seguenti:

    |Modello di distribuzione di Azure             | sottoscrizione  |
    |---------                          |---------|
    |Entrambi Resource Manager              |[Uguale](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Diversa](create-peering-different-subscriptions.md)|
    |Uno di Resource Manager, uno della versione classica  |[Uguale](create-peering-different-deployment-models.md)|
    |                                   |[Diversa](create-peering-different-deployment-models-subscriptions.md)|

* Per informazioni su come creare una topologia di rete hub-spoke, vedere [topologia di rete hub-spoke in Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Per informazioni su tutte le impostazioni del peering di rete virtuale, vedere [creare, modificare o eliminare un peering di rete virtuale](virtual-network-manage-peering.md).
* Per risposte al peering di rete virtuale comune e a domande sul peering di rete virtuale globale, vedere [peering di VNet](virtual-networks-faq.md#vnet-peering).
