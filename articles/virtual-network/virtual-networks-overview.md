---
title: Rete virtuale di Azure | Microsoft Docs
description: "Informazioni sui concetti e sulle funzionalità di Rete virtuale di Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2017
ms.author: jdial
ms.openlocfilehash: 892aa03bd058b50fc4868a225dfe602624ff19ef
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="azure-virtual-network"></a>Rete virtuale di Azure

Il servizio Rete virtuale di Microsoft Azure consente alle risorse di Azure di comunicare in modo sicuro tra loro in una rete virtuale. Una rete virtuale è un isolamento logico del cloud di Azure dedicato alla sottoscrizione. È possibile connettere reti virtuali ad altre reti virtuali o alla rete locale. L'immagine seguente mostra alcune funzionalità del servizio Rete virtuale di Azure:

![Diagramma di rete](./media/virtual-networks-overview/virtual-network-overview.png)

Per altre informazioni sulle funzionalità di Rete virtuale di Azure riportate di seguito, fare clic sulla funzionalità.
- **[Isolamento:](#isolation)** le reti virtuali sono isolate una dall'altra. È possibile creare reti virtuali separate per sviluppo, test e produzione che usano gli stessi blocchi di indirizzi CIDR (ad esempio, 10.0.0.0/0). È altrimenti possibile creare più reti virtuali che usano blocchi di indirizzi CIDR diversi e connettere tra loro le reti. Una rete virtuale può essere segmentata in più subnet. Azure consente la risoluzione dei nomi interna per le risorse distribuite in una rete virtuale. Se necessario, è possibile configurare una rete virtuale in modo da usare server DNS personalizzati anziché la risoluzione dei nomi interna di Azure.
- **[Comunicazione Internet:](#internet)** per impostazione predefinita, le risorse, ad esempio le macchine virtuali distribuire in una rete virtuale, hanno accesso a Internet. È anche possibile abilitare l'accesso in ingresso a risorse specifiche, se necessario.
- **[Comunicazione delle risorse di Azure:](#within-vnet)**  le risorse di Azure distribuite in una rete virtuale possono comunicare tra loro usando indirizzi IP privati, anche se sono distribuite in subnet diverse. Azure offre il routing predefinito tra subnet, reti virtuali connesse e reti locali, quindi non è necessario configurare e gestire le route. Se si vuole, è possibile personalizzare il routing di Azure.
- **[Connettività delle reti virtuali:](#connect-vnets)**  le reti virtuali possono essere connesse tra loro, in modo da consentire alle risorse in una rete virtuale di comunicare con le risorse in qualsiasi altra rete virtuale.
- **[Connettività locale:](#connect-on-premises)**  una rete virtuale può essere connessa a una rete locale, consentendo alle risorse di comunicare tra loro.
- **[Applicazione di filtri al traffico:](#filtering)**  è possibile filtrare il traffico di rete da e verso le risorse in una rete virtuale per indirizzo IP di origine e porta, per indirizzo IP di destinazione e porta e per protocollo.
- **[Routing:](#routing)** facoltativamente, è possibile sostituire il routing predefinito di Azure configurando route personalizzate o propagando route BGP attraverso un gateway di rete.

## <a name = "isolation"></a>Isolamento e segmentazione delle reti

È possibile implementare più reti virtuali in ogni [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [area](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) di Azure. Ogni rete virtuale è isolata dalle altre. Per ogni rete virtuale è possibile:
- Specificare uno spazio indirizzi IP privato personalizzato con indirizzi pubblici e privati (RFC 1918). Azure assegna alle risorse di una rete virtuale un indirizzo IP privato dello spazio indirizzi specificato.
- Segmentare la rete virtuale in una o più subnet e allocare una parte dello spazio indirizzi della rete virtuale a ogni subnet.
- Usare la risoluzione dei nomi fornita da Azure oppure specificare un server DNS personalizzato destinato all'uso da parte delle risorse in una rete virtuale. Per altre informazioni sulla risoluzione dei nomi nelle reti virtuali, vedere l'articolo [Risoluzione dei nomi per le risorse nelle reti virtuali](virtual-networks-name-resolution-for-vms-and-role-instances.md).

## <a name = "internet"></a>Comunicazione Internet
Tutte le risorse in una rete virtuale possono comunicare verso l'esterno con Internet. Per impostazione predefinita, l'indirizzo IP privato della risorsa viene convertito (con Source Network Address Translation, SNAT) in un indirizzo IP pubblico selezionato dall'infrastruttura di Azure. Per altre informazioni sulla connettività Internet in uscita, vedere l'articolo [Informazioni sulle connessioni in uscita in Azure](..\load-balancer\load-balancer-outbound-connections.md). Per impedire la connettività Internet in uscita, è possibile implementare route personalizzate o il filtro del traffico.

Per la comunicazione in ingresso verso le risorse di Azure da Internet o la comunicazione in uscita verso Internet senza SNAT, è necessario che a una risorsa sia assegnato un indirizzo IP pubblico. Per altre informazioni sugli indirizzi IP pubblici, vedere l'articolo [Indirizzi IP pubblici](virtual-network-public-ip-address.md).

## <a name="within-vnet"></a>Proteggere la comunicazione tra le risorse di Azure

È possibile distribuire macchine virtuali all'interno di una rete virtuale. Le macchine virtuali comunicano con altre risorse in una rete virtuale tramite un'interfaccia di rete. Per altre informazioni sulle interfacce di rete, vedere [Interfacce di rete](virtual-network-network-interface.md).

È anche possibile distribuire diversi altri tipi di risorse di Azure in una rete virtuale, ad esempio ambienti del servizio app di Azure e set di scalabilità di macchine virtuali di Azure. Per un elenco completo delle risorse di Azure che è possibile distribuire in una rete virtuale, vedere l'articolo relativo all'[integrazione del servizio di rete virtuale per i servizi di Azure](virtual-network-for-azure-services.md).

Alcune risorse non possono essere distribuite in una rete virtuale, ma consentono di limitare la comunicazione alle sole risorse all'interno di una rete virtuale. Per altre informazioni su come limitare l'accesso alle risorse, vedere [Endpoint del servizio Rete virtuale](virtual-network-service-endpoints-overview.md). 

## <a name="connect-vnets"></a>Connettere reti virtuali

È possibile connettere le reti virtuali tra loro, in modo da consentire alle risorse in qualsiasi rete virtuale di comunicare con le altre mediante il peering di rete virtuale. La larghezza di banda e la latenza di comunicazione tra le risorse in diverse reti virtuali rimangono uguali a quella tra le risorse connesse alla stessa rete virtuale. Per altre informazioni sul peering, vedere l'articolo [Peering reti virtuali](virtual-network-peering-overview.md).

## <a name="connect-on-premises"></a>Connettersi a una rete locale

È possibile connettere la rete locale a una rete virtuale usando qualsiasi combinazione delle opzioni seguenti:
- **Rete privata virtuale (VPN) da punto a sito:** viene stabilita tra una rete virtuale e un singolo PC nella rete. Per ogni PC per cui si vuole stabilire la connettività con una rete virtuale è necessario configurare la connessione in modo indipendente. Questo tipo di connessione è ideale se si sta appena iniziando a usare Azure o per gli sviluppatori, perché richiede modifiche minime o nessuna modifica alla rete esistente. La connessione usa il protocollo SSTP per fornire la comunicazione crittografata su Internet tra il PC e una rete virtuale. La latenza per una VPN da punto a sito è imprevedibile, dal momento che il traffico attraversa Internet.
- **VPN da sito a sito:** viene stabilita tra il dispositivo VPN e un gateway VPN di Azure distribuito in una rete virtuale. Questo tipo di connessione consente a qualsiasi risorsa locale autorizzata dall'utente di accedere a una rete virtuale. La connessione è una VPN IPSec/IKE che fornisce la comunicazione crittografata su Internet tra il dispositivo locale e il gateway VPN di Azure. La latenza per una connessione da sito a sito è imprevedibile, perché il traffico attraversa Internet.
- **Azure ExpressRoute:** viene stabilita una connessione tra la rete e Azure tramite un partner ExpressRoute. La connessione è privata. Il traffico non attraversa Internet. La latenza per una connessione ExpressRoute è prevedibile, perché il traffico non attraversa Internet.

Per altre informazioni su tutte le opzioni di connessione riportate sopra, vedere [Diagrammi di topologia di connessione](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).

## <a name="filtering"></a>Filtrare il traffico di rete
È possibile filtrare il traffico di rete tra subnet usando una o entrambe le opzioni seguenti.
- **Gruppi di sicurezza di rete:** un gruppo di sicurezza di rete può contenere più regole di sicurezza in ingresso e in uscita che consentono di filtrare il traffico per protocollo, porta e indirizzo IP di origine e di destinazione. È possibile applicare un gruppo di sicurezza di rete a ogni interfaccia di rete in una macchina virtuale. È inoltre possibile applicare un gruppo di sicurezza di rete alla subnet a cui appartiene un'interfaccia di rete o un'altra risorse di Azure. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza di rete](security-overview.md#network-security-groups).
- **Appliance virtuale di rete:** un'appliance virtuale di rete è una macchina virtuale che esegue software con una funzione di rete, ad esempio un firewall. Per un elenco delle appliance virtuali di rete disponibili, vedere [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Sono anche disponibili appliance virtuali di rete che eseguono l'ottimizzazione WAN e altre funzioni per il traffico di rete. Le appliance virtuali di rete vengono in genere usate con route BGP o definite dall'utente. È inoltre possibile usare un'appliance virtuale di rete per filtrare il traffico tra le reti virtuali.

## <a name="routing"></a>Instradare il traffico di rete

Per impostazione predefinita, Azure crea tabelle di route che consentono alle risorse connesse a qualsiasi subnet di qualsiasi rete virtuale di comunicare tra loro e con Internet. Per sostituire le route predefinite create da Azure, è possibile implementare una o entrambe le opzioni seguenti.
- **Route definite dall'utente:** è possibile creare tabelle di route personalizzate con route che controllano l'instradamento del traffico per ogni subnet. Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente](virtual-networks-udr-overview.md#user-defined).
- **Route BGP:** se si connette la rete virtuale alla rete locale con un gateway VPN di Azure o una connessione ExpressRoute, è possibile propagare le route BGP alle reti virtuali.

## <a name="pricing"></a>Prezzi

Per le reti virtuali, le subnet, le tabelle di route e i gruppi di sicurezza di rete non è previsto alcun addebito. All'utilizzo della larghezza di banda Internet in uscita, agli indirizzi IP pubblici, al peering reti virtuali, ai gateway VPN e a ExpressRoute vengono applicate specifiche strutture di prezzi. Per altre informazioni, vedere le pagine relative ai prezzi di [Rete virtuale](https://azure.microsoft.com/pricing/details/virtual-network), [Gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway) ed [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="faq"></a>Domande frequenti

Per esaminare le domande frequenti su Rete virtuale di Azure, vedere l'articolo [Domande frequenti su Rete virtuale](virtual-networks-faq.md).

## <a name="next-steps"></a>Passaggi successivi

- Creare la prima rete virtuale e distribuirvi alcune macchine virtuali seguendo la procedura descritta in [Creare la prima rete virtuale](quick-create-portal.md).
- Creare una connessione da punto a sito a una rete virtuale seguendo la procedura descritta in [Configurare una connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) chiave di Azure.
