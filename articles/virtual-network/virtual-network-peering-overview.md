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
ms.openlocfilehash: 5fb54e812e72b9393ffdf632085d0f32ab8b1988
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279546"
---
# <a name="virtual-network-peering"></a>Peering di rete virtuale

Il peering di reti virtuali consente di connettere senza problemi le reti in Rete virtuale di [Azure.](virtual-networks-overview.md) Le reti virtuali vengono visualizzate come un'unica soluzione per scopi di connettività. Il traffico tra macchine virtuali utilizza l'infrastruttura backbone Microsoft.The traffic between virtual machines uses the Microsoft backbone infrastructure. Come il traffico tra macchine virtuali nella stessa rete, il traffico viene instradato solo attraverso la rete privata di Microsoft.Like traffic between virtual machines in the same network, traffic is routed through Microsoft's *private* network only.

Azure supporta i tipi di peering seguenti:Azure supports the following types of peering:

* Peering di rete virtuale: connettere reti virtuali all'interno della stessa area di Azure.Virtual network peering: Connect virtual networks within the same Azure region.
* Peering globale della rete virtuale: connessione di reti virtuali tra aree di Azure.Global virtual network peering: Connecting virtual networks across Azure regions.

Il peering reti virtuali, locale o globale, include i vantaggi seguenti:

* Connessione a bassa latenza e larghezza di banda elevata tra le risorse in reti virtuali diverse.
* Possibilità per le risorse in una rete virtuale di comunicare con le risorse in una rete virtuale diversa.
* Possibilità di trasferire dati tra reti virtuali tra sottoscrizioni di Azure, tenant di Azure Active Directory, modelli di distribuzione e aree di Azure.The ability to transfer data between Azure subscriptions, Azure Active Directory tenants, deployment models, and Azure regions.
* The ability to peer virtual networks created through the Azure Resource Manager.
* La possibilità di eseguire il peernetworkazione di una rete virtuale creata tramite Resource Manager a una creata tramite il modello di distribuzione classica. Per altre informazioni in proposito, vedere le [informazioni sui modelli di distribuzione di Azure](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
* Tempo di inattività nullo per le risorse nelle due reti virtuali durante o dopo la creazione del peering.

Il traffico di rete tra reti virtuali di cui è stato eseguito il peering è privato. Il traffico tra le reti virtuali viene mantenuto nella rete backbone Microsoft. Nella comunicazione tra le reti virtuali non sono necessari gateway, una rete Internet pubblica o la crittografia.

## <a name="connectivity"></a>Connettività

Per le reti virtuali con peered, le risorse in entrambe le reti virtuali possono connettersi direttamente con le risorse nella rete virtuale con peered.

La latenza di rete tra macchine virtuali presenti in reti virtuali di cui è stato eseguito il peering nella stessa area è identica a quella in un'unica rete virtuale. La velocità effettiva della rete si basa sulla larghezza di banda consentita per la macchina virtuale proporzionalmente alle relative dimensioni, senza altre restrizioni alla larghezza di banda consentita nel peering.

Il traffico tra macchine virtuali presenti in reti virtuali di cui è stato eseguito il peering viene direttamente instradato attraverso l'infrastruttura backbone Microsoft, non attraverso un gateway né tramite la rete Internet pubblica.

È possibile applicare gruppi di sicurezza di rete in una rete virtuale per bloccare l'accesso ad altre reti virtuali o subnet.
Quando si configura il peering di rete virtuale, aprire o chiudere le regole del gruppo di sicurezza di rete tra le reti virtuali. Se si apre la connettività completa tra reti virtuali con peered, è possibile applicare gruppi di sicurezza di rete per bloccare o negare l'accesso specifico. La connettività completa è l'opzione predefinita. Per ulteriori informazioni sui gruppi di sicurezza di rete, vedere [Gruppi](security-overview.md)di sicurezza .

## <a name="service-chaining"></a>Concatenamento dei servizi

Il concatenamento dei servizi consente di indirizzare il traffico da una rete virtuale a un'appliance virtuale o a un gateway in una rete con peering tramite route definite dall'utente.

Per abilitare il concatenamento dei servizi, configurare le route definite dall'utente che puntano alle macchine virtuali nelle reti virtuali con peering come indirizzo IP *dell'hop successivo.* Le route definite dall'utente possono anche puntare a gateway di rete virtuale per abilitare il concatenamento dei servizi.

È possibile distribuire reti hub-and-spoke, in cui la rete virtuale hub ospita componenti dell'infrastruttura, ad esempio un'appliance virtuale di rete o un gateway VPN. e tutte le reti virtuali spoke possono eseguire il peering con la rete virtuale dell'hub. Il traffico passa attraverso appliance virtuali di rete o gateway VPN nella rete virtuale dell'hub.

Il peering di rete virtuale consente di usare come hop successivo di una route definita dall'utente l'indirizzo IP di una macchina virtuale nella rete virtuale con peering oppure un gateway VPN. Non è possibile eseguire il route tra reti virtuali con una route definita dall'utente che specifica un gateway di Azure ExpressRoute come tipo di hop successivo. Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente](virtual-networks-udr-overview.md#user-defined). Per informazioni su come creare una topologia di rete hub e spoke, vedere [Topologia di rete Hub-spoke in Azure.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="gateways-and-on-premises-connectivity"></a>Gateway e connettività locale

Ogni rete virtuale, inclusa una rete virtuale con peered, può avere un proprio gateway. Una rete virtuale può usare il proprio gateway per connettersi a una rete locale. È inoltre possibile configurare [connessioni da rete virtuale a rete virtuale](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) usando i gateway, anche per le reti virtuali con peered.

Quando si configurano entrambe le opzioni per l'interconnettività della rete virtuale, il traffico tra le reti virtuali passa attraverso la configurazione di peering. Il traffico usa la backbone di Azure.The traffic uses the Azure backbone.

È anche possibile configurare il gateway nella rete virtuale con peering come punto di transito a una rete locale. In questo caso, la rete virtuale che utilizza un gateway remoto non può avere un proprio gateway. Una rete virtuale dispone di un solo gateway. Il gateway è un gateway locale o remoto nella rete virtuale con peered, come illustrato nel diagramma seguente:The gateway is either a local or remote gateway in the peered virtual network, as shown in the following diagram:

![Peering reti virtuali con transito](./media/virtual-networks-peering-overview/local-or-remote-gateway-in-peered-virual-network.png)

Sia il peering della rete virtuale che il peering della rete virtuale globale supportano il transito del gateway.

È supportato il transito del gateway tra reti virtuali create tramite modelli di distribuzione diversi. Il gateway deve trovarsi nella rete virtuale nel modello di Resource Manager.The gateway must be in the virtual network in the Resource Manager model. Per altre informazioni sull'uso di un gateway per la trasmissione, vedere [Configurare un gateway VPN per il transito nel peering di rete virtuale](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Quando si peernetworkano reti virtuali che condividono una singola connessione Azure ExpressRoute, il traffico tra di esse passa attraverso la relazione di peering. Tale traffico usa la rete backbone di Azure.That traffic uses the Azure backbone network. È comunque possibile continuare a usare i gateway locali in ogni rete virtuale per connettersi al circuito locale. In caso contrario, è possibile usare un gateway condiviso e configurare il transito per la connettività locale.

## <a name="troubleshoot"></a>Risolvere i problemi

Per verificare che le reti virtuali siano sottoposte a peered, è possibile controllare le route valide. Verificare la presenza di un'interfaccia di rete in qualsiasi subnet in una rete virtuale. Se esiste un peering di rete virtuale, tutte le subnet all'interno della rete virtuale hanno route con tipo di hop successivo *Peering reti virtuali* per ogni spazio degli indirizzi di ogni rete virtuale con peering. Per ulteriori informazioni, vedere [Diagnosticare un problema](diagnose-network-routing-problem.md)di routing di una macchina virtuale .

È anche possibile risolvere i problemi di connettività a una macchina virtuale in una rete virtuale con peered usando Watcher di rete di Azure.You can also troubleshoot connectivity to a virtual machine in a peered virtual network using Azure Network Watcher. Un controllo di connettività consente di vedere come viene instradato il traffico dall'interfaccia di rete di una macchina virtuale di origine all'interfaccia di rete di una macchina virtuale di destinazione. Per altre informazioni, vedere Risolvere i problemi relativi alle connessioni con Watcher di rete di Azure usando il portale di Azure.For more information, see [Troubleshoot connections with Azure Network Watcher using the Azure portal.](../network-watcher/network-watcher-connectivity-portal.md#check-connectivity-to-a-virtual-machine)

È anche possibile provare i [problemi di risoluzione dei](virtual-network-troubleshoot-peering-issues.md)problemi di peering della rete virtuale .

## <a name="constraints-for-peered-virtual-networks"></a>Vincoli per le reti virtuali con peeredConstraints for peered virtual networks<a name="requirements-and-constraints"></a>

I vincoli seguenti si applicano solo quando il peering viene eseguito globalmente nelle reti virtuali:

* Le risorse in una rete virtuale non possono comunicare con l'indirizzo IP front-end di un servizio di bilanciamento del carico interno di base in una rete virtuale con peering globale.
* Alcuni servizi che usano un servizio di bilanciamento del carico di base non funzionano sul peering di rete virtuale globale. Per ulteriori informazioni, vedere [Quali sono i vincoli correlati ai peering della rete virtuale globale e ai servizi di bilanciamento del carico?](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers).

Per altre informazioni, vedere [Requisiti e vincoli](virtual-network-manage-peering.md#requirements-and-constraints). Per ulteriori informazioni sul numero supportato di peering, vedere [Limiti di rete](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="permissions"></a>Autorizzazioni

Per informazioni sulle autorizzazioni necessarie per creare un peering di rete virtuale, vedere [Autorizzazioni](virtual-network-manage-peering.md#permissions).

## <a name="pricing"></a>Prezzi

È previsto un costo nominale per il traffico in ingresso ed in uscita che usa una connessione di peering di rete virtuale. Per ulteriori informazioni, vedere [Prezzi della rete virtuale](https://azure.microsoft.com/pricing/details/virtual-network).

Gateway Transit è una proprietà di peering che consente a una rete virtuale di usare un gateway VPN/ExpressRoute in una rete virtuale con peering. Il transito del gateway funziona sia per la connettività cross-premise che per la connettività da rete a rete. Il traffico verso il gateway (ingresso o uscita) nella rete virtuale con peering comporta costi di peering di rete virtuale nella rete virtuale (o rete virtuale non gateway). Per altre informazioni, vedere Prezzi del gateway VPN per gli addebiti del gateway VPN e Prezzi del gateway ExpressRoute per gli addebiti del gateway ExpressRoute.For more information, see [VPN Gateway pricing](https://azure.microsoft.com/pricing/details/vpn-gateway/) for VPN gateway charges and ExpressRoute Gateway pricing for ExpressRoute gateway charges.

>[!NOTE]
> Una versione precedente di questo documento indicava che i costi di peering di rete virtuale non sarebbero stati applicati sulla rete virtuale a raggio (o non basata su rete gateway) con Gateway Transit. Ora riflette prezzi accurati per pagina dei prezzi.

## <a name="next-steps"></a>Passaggi successivi

* È possibile creare un peering tra due reti virtuali. Le reti possono appartenere alla stessa sottoscrizione, a modelli di distribuzione diversi nella stessa sottoscrizione o a sottoscrizioni diverse. Completare un'esercitazione per uno degli scenari seguenti:

    |Modello di distribuzione di Azure             | Subscription  |
    |---------                          |---------|
    |Entrambi Resource Manager              |[Uguale](tutorial-connect-virtual-networks-portal.md)|
    |                                   |[Diversa](create-peering-different-subscriptions.md)|
    |Uno di Resource Manager, uno della versione classica  |[Uguale](create-peering-different-deployment-models.md)|
    |                                   |[Diversa](create-peering-different-deployment-models-subscriptions.md)|

* Per informazioni su come creare una topologia di rete hub e spoke, vedere [Topologia di rete Hub-spoke in Azure.](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
* Per altre informazioni su tutte le impostazioni di peering della rete virtuale, vedere [Creare, modificare o eliminare un peering](virtual-network-manage-peering.md)di rete virtuale.
* Per le risposte alle domande comuni sul peering di rete virtuale e sul peering di rete virtuale globale, vedere [Peering della rete virtuale](virtual-networks-faq.md#vnet-peering).
