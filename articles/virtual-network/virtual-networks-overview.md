---
title: Rete virtuale di Azure | Microsoft Docs
description: "Informazioni sui concetti e sulle funzionalità di Rete virtuale di Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/23/2017
ms.author: jdial
ms.openlocfilehash: 6d6afd2b9b956138ed400fbd6cabd3b480fde0f0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2017
---
# <a name="azure-virtual-network"></a>Rete virtuale di Azure

Il servizio Rete virtuale di Azure consente di connettere tra loro le risorse di Azure in modo sicuro con reti virtuali. Una rete virtuale è una rappresentazione della propria rete nel cloud. È un isolamento logico del cloud di Azure dedicato alla sottoscrizione. È anche possibile connettere le reti virtuali alla rete locale. L'immagine seguente mostra alcune funzionalità del servizio Rete virtuale di Azure:

![Diagramma di rete](./media/virtual-networks-overview/virtual-network-overview.png)

Per altre informazioni sulle funzionalità di Rete virtuale di Azure riportate di seguito, fare clic sulla funzionalità.
- **[Isolamento:](#isolation)** le reti virtuali sono isolate una dall'altra. È possibile creare reti virtuali separate per sviluppo, test e produzione che usano gli stessi blocchi di indirizzi CIDR. È altrimenti possibile creare più reti virtuali che usano blocchi di indirizzi CIDR diversi e connettere tra loro le reti. Una rete virtuale può essere segmentata in più subnet. Azure offre la risoluzione dei nomi interna per le VM e le istanze del ruolo Servizi cloud connesse a una rete virtuale. Facoltativamente, è possibile configurare una rete virtuale in modo da usare server DNS personalizzati anziché la risoluzione dei nomi interna di Azure.
- **[Connettività Internet:](#internet)** per impostazione predefinita, tutte le macchine virtuali (VM) di Azure e le istanze del ruolo Servizi cloud connesse a una rete virtuale hanno accesso a Internet. È anche possibile abilitare l'accesso in ingresso a risorse specifiche, se necessario.
- **[Connettività delle risorse di Azure:](#within-vnet)** risorse di Azure come Servizi cloud e VM possono essere connesse alla stessa rete virtuale. Le risorse possono essere connesse tra loro usando indirizzi IP privati, anche se si trovano in subnet diverse. Azure offre il routing predefinito tra subnet, reti virtuali e reti locali, quindi non è necessario configurare e gestire le route.
- **[Connettività delle reti virtuali:](#connect-vnets)** le reti virtuali possono essere connesse tra loro, in modo che le risorse connesse a qualsiasi rete virtuale possano comunicare con qualsiasi risorsa in un'altra rete virtuale.
- **[Connettività locale:](#connect-on-premises)** le reti virtuali possono essere connesse a reti locali tramite connessioni di rete private tra la rete e Azure o tramite una connessione VPN da sito a sito su Internet.
- **[Applicazione di filtri al traffico:](#filtering)** il traffico di rete delle VM e delle istanze del ruolo Servizi cloud può essere filtrato in ingresso e in uscita per indirizzo IP e porta di origine, indirizzo IP e porta di destinazione e protocollo.
- **[Routing:](#routing)** facoltativamente, è possibile sostituire il routing predefinito di Azure configurando route personalizzate o usando route BGP attraverso un gateway di rete.

## <a name = "isolation"></a>Isolamento e segmentazione delle reti

È possibile implementare più reti virtuali in ogni [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [area](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) di Azure. Ogni rete virtuale è isolata dalle altre. Per ogni rete virtuale è possibile:
- Specificare uno spazio indirizzi IP privato personalizzato con indirizzi pubblici e privati (RFC 1918). Azure assegna alle risorse connesse alla rete virtuale un indirizzo IP privato dello spazio indirizzi specificato.
- Segmentare la rete virtuale in una o più subnet e allocare una parte dello spazio indirizzi della rete virtuale a ogni subnet.
- Usare la risoluzione dei nomi fornita da Azure oppure specificare un server DNS personalizzato che verrà usato dalle risorse connesse a una rete virtuale. Per altre informazioni sulla risoluzione dei nomi nelle reti virtuali, vedere l'articolo relativo alla [risoluzione dei nomi per VM e Servizi cloud](virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name = "internet"></a>Connettersi a Internet
Per impostazione predefinita, tutte le risorse connesse a una rete virtuale hanno la connettività in uscita a Internet. L'indirizzo IP privato della risorsa viene convertito (con Source Network Address Translation, SNAT) in un indirizzo IP pubblico dall'infrastruttura di Azure. Per altre informazioni sulla connettività Internet in uscita, vedere l'articolo [Informazioni sulle connessioni in uscita in Azure](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address). È possibile modificare la connettività predefinita implementando il routing personalizzato e applicando filtri al traffico.

Per la comunicazione in ingresso verso le risorse di Azure da Internet o la comunicazione in uscita verso Internet senza SNAT, è necessario che a una risorsa sia assegnato un indirizzo IP pubblico. Per altre informazioni sugli indirizzi IP pubblici, vedere l'articolo [Indirizzi IP pubblici](virtual-network-public-ip-address.md).

## <a name="within-vnet"></a>Connettere risorse di Azure
È possibile connettere a una rete virtuale diverse risorse di Azure, ad esempio macchine virtuali (VM), Servizi cloud, ambienti del servizio app e set di scalabilità di macchine virtuali. Le VM si connettono a una subnet di una rete virtuale tramite un'interfaccia di rete. Per altre informazioni sulle interfacce di rete, vedere l'articolo [Interfacce di rete](virtual-network-network-interface.md).

## <a name="connect-vnets"></a>Connettere reti virtuali

È possibile connettere tra loro le reti virtuali in modo che le risorse connesse a una di esse possano comunicare tra loro attraverso le reti virtuali. Per connettere tra loro le reti virtuali è possibile usare una o entrambe le opzioni seguenti.
- **Peering:** consente alle risorse connesse a diverse reti virtuali di Azure nella stessa località di Azure di comunicare tra loro. La larghezza di banda e la latenza tra le reti virtuali sono uguali a quelle che si riscontrerebbero se le risorse fossero connesse alla stessa rete virtuale. Per altre informazioni sul peering, vedere l'articolo [Peering reti virtuali](virtual-network-peering-overview.md).
- **Connessione da rete virtuale a rete virtuale:** abilita le risorse connesse a diverse reti virtuali di Azure nella stessa località o in località diverse. A differenza del peering, la larghezza di banda tra le reti virtuali è limitata perché il traffico deve passare attraverso un gateway VPN di Azure. Per altre informazioni sulla connessione di reti virtuali con una connessione da rete virtuale a rete virtuale, vedere l'articolo [Configurare una connessione da rete virtuale a rete virtuale](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="connect-on-premises"></a>Connettersi a una rete locale

È possibile connettere la rete locale a una rete virtuale usando qualsiasi combinazione delle opzioni seguenti.
- **Rete privata virtuale (VPN) da punto a sito:** viene stabilita tra un singolo PC connesso alla rete e la rete virtuale. Questo tipo di connessione è ideale se si sta appena iniziando a usare Azure o per gli sviluppatori, perché richiede modifiche minime o nessuna modifica alla rete esistente. La connessione usa il protocollo SSTP per fornire la comunicazione crittografata su Internet tra il PC e la rete virtuale. La latenza per una VPN da punto a sito è imprevedibile, dal momento che il traffico attraversa Internet.
- **VPN da sito a sito:** viene stabilita una connessione tra il dispositivo VPN e un gateway VPN di Azure. Questo tipo di connessione consente a qualsiasi risorsa locale autorizzata dall'utente di accedere a una rete virtuale. La connessione è una VPN IPSec/IKE che fornisce la comunicazione crittografata su Internet tra il dispositivo locale e il gateway VPN di Azure. La latenza per una connessione da sito a sito è imprevedibile, perché il traffico attraversa Internet.
- **Azure ExpressRoute:** viene stabilita una connessione tra la rete e Azure tramite un partner ExpressRoute. La connessione è privata. Il traffico non attraversa Internet. La latenza per una connessione ExpressRoute è prevedibile, perché il traffico non attraversa Internet.

Per altre informazioni su tutte le opzioni di connessione riportate sopra, vedere [Diagrammi delle topologie di connessione](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).

## <a name="filtering"></a>Filtrare il traffico di rete
È possibile filtrare il traffico di rete tra subnet usando una o entrambe le opzioni seguenti.
- **Gruppi di sicurezza di rete (NSG):** ogni gruppo di sicurezza di rete contiene più regole di sicurezza in ingresso e in uscita che consentono di filtrare il traffico per protocollo, porta e indirizzo IP di origine e di destinazione. È possibile applicare un gruppo di sicurezza di rete a qualsiasi interfaccia di rete di una VM, nonché alla subnet a cui è connessa un'interfaccia di rete o un'altra risorsa di Azure. Per altre informazioni sui gruppi di sicurezza di rete, vedere l'articolo [Gruppi di sicurezza di rete](virtual-networks-nsg.md).
- **Appliance virtuali di rete:** un'appliance virtuale di rete è una VM che esegue software con un funzione di rete, ad esempio un firewall. Per un elenco delle appliance virtuali di rete disponibili, vedere [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Sono anche disponibili appliance virtuali di rete che eseguono l'ottimizzazione WAN e altre funzioni per il traffico di rete. Le appliance virtuali di rete vengono in genere usate con route BGP o definite dall'utente. È anche possibile usare un'appliance virtuale di rete per filtrare il traffico tra reti virtuali.

## <a name="routing"></a>Instradare il traffico di rete

Per impostazione predefinita, Azure crea tabelle di route che consentono alle risorse connesse a qualsiasi subnet di qualsiasi rete virtuale di comunicare tra loro. Per sostituire le route predefinite create da Azure, è possibile implementare una o entrambe le opzioni seguenti.
- **Route definite dall'utente:** è possibile creare tabelle di route personalizzate con route che controllano l'instradamento del traffico per ogni subnet. Per altre informazioni sulle route definite dall'utente, vedere l'articolo [Route definite dall'utente](virtual-networks-udr-overview.md).
- **Route BGP:** se si connette la rete virtuale alla rete locale con un gateway VPN di Azure o una connessione ExpressRoute, è possibile propagare le route BGP alle reti virtuali.

## <a name="pricing"></a>Prezzi

Per le reti virtuali, le subnet, le tabelle di route e i gruppi di sicurezza di rete non è previsto alcun addebito. All'utilizzo della larghezza di banda Internet in uscita, agli indirizzi IP pubblici, al peering reti virtuali, ai gateway VPN e a ExpressRoute vengono applicate specifiche strutture di prezzi. Per altre informazioni, vedere le pagine relative ai prezzi di [Rete virtuale](https://azure.microsoft.com/pricing/details/virtual-network), [Gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway) ed [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="faq"></a>Domande frequenti

Per esaminare le domande frequenti su Rete virtuale, vedere l'articolo [Domande frequenti sulla rete virtuale di Azure](virtual-networks-faq.md).


## <a name="next-steps"></a>Passaggi successivi

- Creare la prima rete virtuale e connettervi alcune VM seguendo la procedura descritta nell'articolo [Creare la prima rete virtuale](virtual-network-get-started-vnet-subnet.md).
- Creare una connessione da punto a sito a una rete virtuale seguendo la procedura descritta nell'articolo [Configurare una connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) chiave di Azure.
