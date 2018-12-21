---
title: Rete virtuale di Azure | Microsoft Docs
description: Informazioni sui concetti e sulle funzionalità di Rete virtuale di Azure.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.assetid: 9633de4b-a867-4ddf-be3c-a332edf02e24
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 87ccc80d36022361ad6191aaf674d38d0f632f10
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321733"
---
# <a name="what-is-azure-virtual-network"></a>Che cos'è Rete virtuale di Azure?

Rete virtuale di Azure consente a diversi tipi di risorse di Azure, ad esempio Macchine virtuali di Azure, di comunicare in modo sicuro tra di esse, con Internet e con le reti locali. L'ambito di una rete virtuale è una singola area; tuttavia, è possibile connettere più reti virtuali da diverse aree usando il peering di rete virtuale.

Rete virtuale di Azure offre le funzionalità chiave seguenti:

## <a name="isolation-and-segmentation"></a>Isolamento e segmentazione

È possibile implementare più reti virtuali in ogni [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) e [area](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) di Azure. Ogni rete virtuale è isolata dalle altre. Per ogni rete virtuale è possibile:
- Specificare uno spazio indirizzi IP privato personalizzato con indirizzi pubblici e privati (RFC 1918). Azure assegna alle risorse di una rete virtuale un indirizzo IP privato dello spazio indirizzi specificato.
- Segmentare la rete virtuale in una o più subnet e allocare una parte dello spazio indirizzi della rete virtuale a ogni subnet.
- Usare la risoluzione dei nomi fornita da Azure oppure specificare un server DNS personalizzato destinato all'uso da parte delle risorse in una rete virtuale.

## <a name="communicate-with-the-internet"></a>Comunicare con Internet

Per impostazione predefinita, tutte le risorse in una rete virtuale possono comunicare verso l'esterno su Internet. Per la comunicazione in ingresso con una risorsa, è possibile assegnarle un indirizzo IP pubblico o un servizio di bilanciamento del carico pubblico. Si può usare un indirizzo IP pubblico o un servizio di bilanciamento del carico pubblico anche per gestire le connessioni in uscita.  Per altre informazioni sulle connessioni in uscita in Azure, vedere [Connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md), [Indirizzi IP pubblici](virtual-network-public-ip-address.md) e [Informazioni su Azure Load Balancer](../load-balancer/load-balancer-overview.md).

>[!NOTE]
>Quando si usa solo un'istanza di [Load Balancer Standard](../load-balancer/load-balancer-standard-overview.md) interna, la connettività in uscita non è disponibile finché non si definisce la modalità di funzionamento desiderata per le [connessioni in uscita](../load-balancer/load-balancer-outbound-connections.md) con un IP pubblico a livello di istanza o un servizio di bilanciamento del carico pubblico.

## <a name="communicate-between-azure-resources"></a>Comunicare tra risorse di Azure

Le risorse di Azure comunicano in modo sicuro tra di esse in uno dei modi seguenti:

- **Tramite una rete virtuale**: è possibile distribuire macchine virtuali e diversi altri tipi di risorse di Azure in una rete virtuale, ad esempio ambienti del servizio app di Azure, servizio Azure Kubernetes e set di scalabilità di macchine virtuali di Azure. Per visualizzare un elenco completo delle risorse di Azure che è possibile distribuire in una rete virtuale, vedere [Integrazione del servizio di rete virtuale](virtual-network-for-azure-services.md). 
- **Tramite un endpoint del servizio di rete virtuale**: estendere lo spazio di indirizzi privato della rete virtuale e l'identità della rete virtuale alle risorse dei servizi di Azure, ad esempio gli account di archiviazione di Azure e i database SQL di Azure, tramite una connessione diretta. Gli endpoint servizio consentono di associare le risorse critiche dei servizi di Azure solo a una rete virtuale. Per altre informazioni, vedere [Panoramica degli endpoint del servizio Rete virtuale](virtual-network-service-endpoints-overview.md).
 
## <a name="communicate-with-on-premises-resources"></a>Comunicare con le risorse locali

È possibile connettere i computer e le reti locali a una rete virtuale usando qualsiasi combinazione delle opzioni seguenti:

- **Rete privata virtuale (VPN) da punto a sito:** viene stabilita tra una rete virtuale e un singolo computer nella rete. Per ogni computer per cui si vuole stabilire la connettività con una rete virtuale è necessario configurare la connessione. Questo tipo di connessione è ideale se si sta appena iniziando a usare Azure o per gli sviluppatori, perché richiede modifiche minime o nessuna modifica alla rete esistente. La comunicazione tra il computer e una rete virtuale viene inviata attraverso un tunnel crittografato tramite Internet. Per altre informazioni, vedere [VPN da punto a sito](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S).
- **VPN da sito a sito:** viene stabilita tra il dispositivo VPN locale e un gateway VPN di Azure distribuito in una rete virtuale. Questo tipo di connessione consente a qualsiasi risorsa locale autorizzata dall'utente di accedere a una rete virtuale. La comunicazione tra il dispositivo VPN locale e un gateway VPN di Azure viene inviata attraverso un tunnel crittografato tramite Internet. Per altre informazioni, vedere [VPN da sito a sito](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti).
- **Azure ExpressRoute:** viene stabilita una connessione tra la rete e Azure tramite un partner ExpressRoute. La connessione è privata. Il traffico non passa da Internet. Per altre informazioni, vedere [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute).

## <a name="filter-network-traffic"></a>Filtrare il traffico di rete
È possibile filtrare il traffico di rete tra subnet usando una o entrambe le opzioni seguenti.
- **Gruppi di sicurezza:** gruppi di sicurezza di rete e gruppi di sicurezza delle applicazioni possono contenere più regole di sicurezza in ingresso e in uscita che consentono di filtrare il traffico verso e dalle risorse per protocollo, porta e indirizzo IP di origine e di destinazione. Per altre informazioni, consultare [Gruppi di sicurezza di rete](security-overview.md#network-security-groups) e [Gruppi di sicurezza delle applicazioni](security-overview.md#application-security-groups).
- **Appliance virtuali di rete:** un'appliance virtuale di rete è una macchina virtuale che esegue una funzione di rete, ad esempio un firewall, un'ottimizzazione WAN o un'altra funzione di rete. Per visualizzare un elenco delle appliance virtuali di rete disponibili che è possibile distribuire in una rete virtuale di Azure, vedere [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances).

## <a name="route-network-traffic"></a>Indirizzare il traffico di rete

Azure instrada il traffico tra subnet, reti virtuali connesse, reti locali e Internet, per impostazione predefinita. Per sostituire le route predefinite create da Azure, è possibile implementare una o entrambe le opzioni seguenti.
- **Tabelle di route:** è possibile creare tabelle di route personalizzate con route che controllano dove viene instradato il traffico per ogni subnet. Altre informazioni sulle [tabelle di route](virtual-networks-udr-overview.md#user-defined).
- **Route Border Gateway Protocol (BGP):** se si connette la rete virtuale alla rete locale con un gateway VPN di Azure o una connessione ExpressRoute, è possibile propagare le route BGP locali alle reti virtuali. Altre informazioni sull'uso di BGP con [Gateway VPN di Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ed [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="connect-virtual-networks"></a>Connettere reti virtuali

È possibile connettere le reti virtuali tra di esse, per poter consentire alle risorse in qualsiasi rete virtuale di comunicare con le altre usando il peering di rete virtuale. Le reti virtuali connesse possono essere in aree di Azure uguali o diversi. Per altre informazioni, vedere [Peering di rete virtuale](virtual-network-peering-overview.md).

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stata presentata una panoramica di Rete virtuale di Azure. Per iniziare a usare una rete virtuale, crearne una, distribuirvi alcune macchine virtuali e comunicare tra le macchine virtuali. Per informazioni sulla procedura, vedere la guida introduttiva [Creare una rete virtuale](quick-create-portal.md).
