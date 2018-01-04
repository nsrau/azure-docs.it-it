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
ms.openlocfilehash: 6cc7035e798ef72f69958a7536a741f80939d4fe
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="azure-virtual-network"></a>Rete virtuale di Azure

Il servizio di rete virtuale di Microsoft Azure consente alle risorse di Azure di comunicare in modo sicuro tra loro in una rete virtuale. Una rete virtuale è un isolamento logico del cloud di Azure dedicato alla sottoscrizione. È possibile connettere reti virtuali ad altre reti virtuali o alla rete locale. L'immagine seguente mostra alcune funzionalità del servizio Rete virtuale di Azure:

![Diagramma di rete](./media/virtual-networks-overview/virtual-network-overview.png)

Per altre informazioni sulle funzionalità di Rete virtuale di Azure riportate di seguito, fare clic sulla funzionalità.
- **[Isolamento:](#isolation)** le reti virtuali sono isolate una dall'altra. È possibile creare reti virtuali separate per sviluppo, test e produzione che usano gli stessi blocchi di indirizzi CIDR (ad esempio, 10.0.0.0/0). È altrimenti possibile creare più reti virtuali che usano blocchi di indirizzi CIDR diversi e connettere tra loro le reti. Una rete virtuale può essere segmentata in più subnet. Azure fornisce la risoluzione dei nomi interna per risorse distribuite in una rete virtuale. Se necessario, è possibile configurare una rete virtuale per l'utilizzo dei propri server DNS, anziché utilizzare la risoluzione dei nomi interno di Azure.
- **[Comunicazioni Internet:](#internet)**  risorse, ad esempio le macchine virtuali distribuite in una rete virtuale, hanno accesso a Internet, per impostazione predefinita. È anche possibile abilitare l'accesso in ingresso a risorse specifiche, se necessario.
- **[Comunicazione delle risorse di Azure:](#within-vnet)**  risorse di Azure distribuite in una rete virtuale possono comunicare tra loro utilizzando indirizzi IP privati, anche se le risorse vengono distribuite in subnet diverse. Azure offre predefinito routing tra subnet, reti virtuali connesse e reti locali, in modo non è necessario configurare e gestire le route. Se si desidera, è possibile personalizzare il routing di Azure.
- **[Connettività delle reti virtuali:](#connect-vnets)**  le reti virtuali possono essere connesse tra loro, in modo da consentire alle risorse in una rete virtuale di comunicare con le risorse in qualsiasi altra rete virtuale.
- **[Connettività locale:](#connect-on-premises)**  una rete virtuale può essere connesso a una rete locale, l'abilitazione di risorse comunicare tra loro.
- **[Filtrare il traffico:](#filtering)**  è possibile filtrare il traffico di rete da e verso le risorse in una rete virtuale per porta e indirizzo IP di origine, indirizzo IP di destinazione e la porta e protocollo.
- **[Routing:](#routing)** facoltativamente, è possibile sostituire il routing predefinito di Azure configurando route personalizzate o propagando route BGP attraverso un gateway di rete.

## <a name = "isolation"></a>Isolamento e segmentazione delle reti

È possibile implementare più reti virtuali in ogni [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [area](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) di Azure. Ogni rete virtuale è isolata dalle altre. Per ogni rete virtuale è possibile:
- Specificare uno spazio indirizzi IP privato personalizzato con indirizzi pubblici e privati (RFC 1918). Azure assegna alle risorse di una rete virtuale un indirizzo IP privato dello spazio indirizzi specificato.
- Segmentare la rete virtuale in una o più subnet e allocare una parte dello spazio indirizzi della rete virtuale a ogni subnet.
- Usare la risoluzione dei nomi fornita da Azure oppure specificare un server DNS per l'utilizzo da risorse in una rete virtuale. Per ulteriori informazioni sulla risoluzione dei nomi in reti virtuali, vedere [la risoluzione dei nomi per le risorse in reti virtuali](virtual-networks-name-resolution-for-vms-and-role-instances.md) articolo.

## <a name = "internet"></a>Comunicazione Internet
Tutte le risorse in una rete virtuale possono comunicare in uscita a Internet. Per impostazione predefinita, l'indirizzo IP privato della risorsa è origine indirizzo di rete convertito (SNAT) a un indirizzo IP pubblico selezionato per l'infrastruttura di Azure. Per altre informazioni sulla connettività Internet in uscita, vedere l'articolo [Informazioni sulle connessioni in uscita in Azure](..\load-balancer\load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json#standalone-vm-with-no-instance-level-public-ip-address). Per impedire la connettività Internet in uscita, è possibile implementare route personalizzate o il filtro del traffico.

Per la comunicazione in ingresso verso le risorse di Azure da Internet o la comunicazione in uscita verso Internet senza SNAT, è necessario che a una risorsa sia assegnato un indirizzo IP pubblico. Per altre informazioni sugli indirizzi IP pubblici, vedere l'articolo [Indirizzi IP pubblici](virtual-network-public-ip-address.md).

## <a name="within-vnet"></a>Proteggere la comunicazione tra le risorse di Azure

È possibile distribuire macchine virtuali all'interno di una rete virtuale. Le macchine virtuali comunicano con altre risorse in una rete virtuale tramite un'interfaccia di rete. Per altre informazioni sulle interfacce di rete, vedere [Interfacce di rete](virtual-network-network-interface.md).

È inoltre possibile distribuire diversi altri tipi di risorse di Azure a una rete virtuale, ad esempio gli ambienti di servizio App di Azure e set di scalabilità di macchine virtuali di Azure. Per un elenco completo delle risorse di Azure che è possibile distribuire in una rete virtuale, vedere l'articolo relativo all'[integrazione del servizio di rete virtuale per i servizi di Azure](virtual-network-for-azure-services.md).

Alcune risorse non possono essere distribuite in una rete virtuale, ma consentono di limitare la comunicazione alle risorse all'interno di una rete virtuale solo. Per ulteriori informazioni su come limitare l'accesso alle risorse, vedere [gli endpoint di servizio di rete virtuale](virtual-network-service-endpoints-overview.md). 

## <a name="connect-vnets"></a>Connettere reti virtuali

È possibile connettere le reti virtuali tra loro, in modo da consentire alle risorse in qualsiasi rete virtuale di comunicare con le altre mediante il peering di rete virtuale. La larghezza di banda e la latenza di comunicazione tra le risorse in diverse reti virtuali rimangono uguali a quella tra le risorse connesse alla stessa rete virtuale. Per altre informazioni sul peering, vedere l'articolo [Peering reti virtuali](virtual-network-peering-overview.md).

## <a name="connect-on-premises"></a>Connettersi a una rete locale

È possibile connettere la rete locale a una rete virtuale usando qualsiasi combinazione delle opzioni seguenti:
- **Rete privata virtuale (VPN) da punto a sito:** viene stabilita tra una rete virtuale e un singolo PC nella rete. Ogni computer che si desidera stabilire la connessione con una rete virtuale è necessario configurare la connessione in modo indipendente. Questo tipo di connessione è ideale se si sta appena iniziando a usare Azure o per gli sviluppatori, perché richiede modifiche minime o nessuna modifica alla rete esistente. La connessione usa il protocollo SSTP per fornire la comunicazione crittografata su Internet tra il PC e una rete virtuale. La latenza per una VPN da punto a sito è imprevedibile, dal momento che il traffico attraversa Internet.
- **VPN da sito a sito:** viene stabilita tra il dispositivo VPN e un gateway VPN di Azure distribuito in una rete virtuale. Questo tipo di connessione consente a qualsiasi risorsa locale autorizzata dall'utente di accedere a una rete virtuale. La connessione è una VPN IPSec/IKE che fornisce la comunicazione crittografata su Internet tra il dispositivo locale e il gateway VPN di Azure. La latenza per una connessione da sito a sito è imprevedibile, perché il traffico attraversa Internet.
- **Azure ExpressRoute:** viene stabilita una connessione tra la rete e Azure tramite un partner ExpressRoute. La connessione è privata. Il traffico non attraversa Internet. La latenza per una connessione ExpressRoute è prevedibile, perché il traffico non attraversa Internet.

Per ulteriori informazioni su tutte le opzioni di connessione precedente, vedere [diagrammi della topologia di connessione](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#diagrams).

## <a name="filtering"></a>Filtrare il traffico di rete
È possibile filtrare il traffico di rete tra subnet usando una o entrambe le opzioni seguenti.
- **Gruppi di sicurezza di rete:** un gruppo di sicurezza di rete può contenere più regole di sicurezza in ingresso e in uscita che consentono di filtrare il traffico per protocollo, porta e indirizzo IP di origine e di destinazione. È possibile applicare un gruppo di sicurezza di rete a ogni interfaccia di rete in una macchina virtuale. È inoltre possibile applicare un gruppo di sicurezza di rete alla subnet a cui appartiene un'interfaccia di rete o un'altra risorse di Azure. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Gruppi di sicurezza di rete](security-overview.md#network-security-groups).
- **Appliance virtuale di rete:** un'appliance virtuale di rete è una macchina virtuale che esegue software con una funzione di rete, ad esempio un firewall. Per un elenco delle appliance virtuali di rete disponibili, vedere [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances). Sono anche disponibili appliance virtuali di rete che eseguono l'ottimizzazione WAN e altre funzioni per il traffico di rete. Le appliance virtuali di rete vengono in genere usate con route BGP o definite dall'utente. È inoltre possibile usare un'appliance virtuale di rete per filtrare il traffico tra le reti virtuali.

## <a name="routing"></a>Instradare il traffico di rete

Azure crea le tabelle di route che consentono di risorse connesse a una qualsiasi subnet di una rete virtuale per comunicare tra loro e Internet, per impostazione predefinita. Per sostituire le route predefinite create da Azure, è possibile implementare una o entrambe le opzioni seguenti.
- **Route definite dall'utente:** è possibile creare tabelle di route personalizzate con route che controllano l'instradamento del traffico per ogni subnet. Per altre informazioni sulle route definite dall'utente, vedere [Route definite dall'utente](virtual-networks-udr-overview.md#user-defined).
- **Route BGP:** se si connette la rete virtuale alla rete locale con un gateway VPN di Azure o una connessione ExpressRoute, è possibile propagare le route BGP alle reti virtuali.

## <a name="pricing"></a>Prezzi

Per le reti virtuali, le subnet, le tabelle di route e i gruppi di sicurezza di rete non è previsto alcun addebito. All'utilizzo della larghezza di banda Internet in uscita, agli indirizzi IP pubblici, al peering reti virtuali, ai gateway VPN e a ExpressRoute vengono applicate specifiche strutture di prezzi. Per altre informazioni, vedere le pagine relative ai prezzi di [Rete virtuale](https://azure.microsoft.com/pricing/details/virtual-network), [Gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway) ed [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="faq"></a>Domande frequenti

Per esaminare le domande frequenti su Rete virtuale di Azure, vedere l'articolo [Domande frequenti su Rete virtuale](virtual-networks-faq.md).

## <a name="next-steps"></a>Passaggi successivi

- Creare la prima rete virtuale e distribuirvi alcune macchine virtuali seguendo la procedura descritta in [Creare la prima rete virtuale](virtual-network-get-started-vnet-subnet.md).
- Creare una connessione da punto a sito a una rete virtuale seguendo la procedura descritta in [Configurare una connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) chiave di Azure.
