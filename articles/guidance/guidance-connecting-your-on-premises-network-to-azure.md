---
title: Connessione della rete locale ad Azure | Microsoft Docs
description: Illustra e confronta i diversi metodi disponibili per la connessione a servizi cloud Microsoft come Azure, Office 365 e Dynamics CRM Online.
services: 
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: 
ms.assetid: 294d39ad-40e0-476a-a362-57911b1172b7
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/25/2016
ms.author: cherylmc
ms.openlocfilehash: 37d83d3b6dea1763d85f2411816ba2fee4279100
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/16/2018
---
# <a name="connecting-your-on-premises-network-to-azure"></a>Connessione della rete locale ad Azure
Microsoft offre diversi tipi di servizi cloud. Benché sia possibile connettersi a tutti i servizi tramite Internet pubblico, è anche possibile connettersi ad alcuni servizi tramite un tunnel VPN su Internet tramite una connessione privata diretta a Microsoft. Questo articolo fornisce informazioni utili per individuare l'opzione di connettività ottimale per le esigenze specifiche in base ai tipi di servizi cloud Microsoft utilizzati. La maggior parte delle organizzazioni usa più tipi di connessione, illustrati più avanti.

## <a name="connecting-over-the-public-internet"></a>Connessione tramite Internet pubblico
Questo tipo di connessione fornisce l'accesso ai servizi cloud Microsoft direttamente tramite Internet, come mostrato di seguito.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Questa connessione è in genere il primo tipo usato per connettersi ai servizi cloud Microsoft. La tabella seguente elenca i vantaggi e gli svantaggi di questo tipo di connessione.

| **Vantaggi** | **Considerazioni** |
| --- | --- |
| Non richiede alcuna modifica alla rete locale, se tutti i dispositivi client hanno accesso senza limiti a tutti gli indirizzi IP e a tutte le porte su Internet. |Anche se il traffico è spesso crittografato tramite HTTPS, può essere intercettato in transito, poiché attraversa Internet pubblico. |
| Possibilità di connessione a tutti i servizi cloud Microsoft esposti su Internet pubblico. |Latenza imprevedibile perché la connessione attraversa Internet. |
| Usa la connessione Internet esistente. | |
| Non richiede la gestione di alcun dispositivo per la connettività. | |

Questa connessione non comporta alcun costo per connettività o larghezza di banda perché si usa la connessione Internet esistente.

## <a name="connecting-with-a-point-to-site-connection"></a>Connessione tramite una connessione da punto a sito
Questo tipo di connessione fornisce l'accesso ad alcuni servizi cloud Microsoft tramite un tunnel SSTP (Secure Socket Tunneling Protocol) su Internet, come mostrato di seguito.

![Connessione da punto a sito](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "Connessione da punto a sito")

La connessione viene stabilita tramite la connessione Internet esistente, ma richiede l'uso di un gateway VPN di Azure. La tabella seguente elenca i vantaggi e gli svantaggi di questo tipo di connessione.

| **Vantaggi** | **Considerazioni** |
| --- | --- |
| Non richiede alcuna modifica alla rete locale, se tutti i dispositivi client hanno accesso senza limiti a tutti gli indirizzi IP e a tutte le porte su Internet. |Anche se il traffico è crittografato tramite IPSec, può essere intercettato in transito, poiché attraversa Internet pubblico. |
| Usa la connessione Internet esistente. |Latenza imprevedibile perché la connessione attraversa Internet. |
| Velocità effettiva massima di 200 Mb/s per gateway. |Richiede la creazione e la gestione di connessioni separate tra ogni dispositivo nella rete locale e ogni gateway a cui deve connettersi ogni dispositivo. |
| Può essere usata per connettersi a servizi di Azure che possono essere connessi a una Rete virtuale di Azure, ad esempio Macchine virtuali di Azure e Servizi cloud di Azure. |Richiede attività di amministrazione minime continuative di un gateway VPN di Azure. |
| Non può essere usata per connettersi a Microsoft Office 365 o Dynamics CRM Online. | |
| Non può essere usata per connettersi ai servizi di Azure che non possono essere connessi a una rete virtuale. | |

Altre informazioni sul servizio [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md), sui rispettivi [prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway) e sui [prezzi](https://azure.microsoft.com/pricing/details/data-transfers) per il trasferimento dei dati in uscita.

## <a name="connecting-with-a-site-to-site-connection"></a>Connessione tramite una connessione da sito a sito
Questo tipo di connessione fornisce l'accesso ad alcuni servizi cloud Microsoft tramite un tunnel IPSec su Internet, come mostrato di seguito.

![Connessione da sito a sito](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "Connessione da sito a sito")

La connessione viene stabilita tramite la connessione Internet esistente, ma richiede l'uso di un gateway VPN di Azure con i rispettivi prezzi associati e i prezzi per il trasferimento dei dati in uscita. La tabella seguente elenca i vantaggi e gli svantaggi di questo tipo di connessione.

| **Vantaggi** | **Considerazioni** |
| --- | --- |
| Tutti i dispositivi sulla rete locale possono comunicare con i servizi di Azure connessi a una rete virtuale, quindi non è necessario configurare connessioni singole per ogni dispositivo. |Anche se il traffico è crittografato tramite IPSec, può essere intercettato in transito, poiché attraversa Internet pubblico. |
| Usa la connessione Internet esistente. |Latenza imprevedibile perché la connessione attraversa Internet. |
| Può essere usata per connettersi a servizi di Azure che possono essere connessi a una Rete virtuale, ad esempio Macchine virtuali e Servizi cloud. |È necessario configurare e gestire un dispositivo VPN* convalidato in locale. |
| Velocità effettiva massima di 200 Mb/s per gateway. |Richiede attività di amministrazione minime continuative di un gateway VPN di Azure. |
| Può forzare il traffico in uscita originato da macchine virtuali cloud attraverso la rete locale per l'analisi e la registrazione tramite route definite dall'utente o BGP (Border Gateway Protocol)**. |Non può essere usata per connettersi a Microsoft Office 365 o Dynamics CRM Online. |
| Non può essere usata per connettersi ai servizi di Azure che non possono essere connessi a una rete virtuale. | |
| Se si usano servizi che avviano connessioni ai dispositivi locali e i criteri di sicurezza lo richiedono, potrebbe essere necessario un firewall tra la rete locale e Azure. | |

* *Visualizzare un elenco di [dispositivi VPN convalidati](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* **Altre informazioni sull'uso di [route definite dall'utente](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) o [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) per imporre il routing da reti virtuali di Azure a un dispositivo locale.

## <a name="connecting-with-a-dedicated-private-connection"></a>Connessione tramite una connessione privata dedicata
Questo tipo di connessione fornisce l'accesso a tutti i servizi cloud Microsoft su una connessione privata dedicata a Microsoft che non attraversa Internet, come mostrato di seguito.

![ExpressRoute](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "Connessione ExpressRoute")

La connessione richiede l'uso del servizio ExpressRoute e una connessione a un provider di connettività. La tabella seguente elenca i vantaggi e gli svantaggi di questo tipo di connessione.

| **Vantaggi** | **Considerazioni** |
| --- | --- |
| Il traffico non può essere intercettato in transito su Internet pubblico, poiché viene usata una connessione dedicata tramite un provider di servizi. |Richiede la gestione di un router locale. |
| Larghezza di banda fino a 10 Gb/s per circuito di ExpressRoute e velocità effettiva fino a 2 Gb/s per ogni gateway. |Richiede una connessione dedicata a un provider di connettività. |
| Latenza prevedibile perché si tratta di una connessione dedicata a Microsoft che non attraversa Internet. |Potrebbe richiedere attività di amministrazione minime continuative di uno o più gateway VPN di Azure, in caso di connessione del circuito a reti virtuali. |
| Non richiede comunicazioni crittografate, anche se è possibile crittografare il traffico, se necessario. |Se si usano servizi cloud che avviano connessioni ai dispositivi locali, potrebbe essere necessario un firewall tra la rete locale e Azure. |
| Possibilità di connessione diretta a tutti i servizi cloud Microsoft, con alcune eccezioni*. |Richiede il protocollo NAT (Network Address Translation) degli indirizzi IP locali che accedono ai data center Microsoft per servizi che non possono essere connessi a una rete virtuale.** |
| Può forzare il traffico in uscita originato da macchine virtuali cloud tramite la rete locale per l'analisi e la registrazione tramite BGP. | |

* *Visualizzare un [elenco di servizi](../expressroute/expressroute-faqs.md#supported-services) che non possono essere usati con ExpressRoute. La sottoscrizione di Azure deve essere approvata per la connessione a Office 365.  Per informazioni dettagliate, vedere l'articolo [Azure ExpressRoute per Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1).
* **Per altre informazioni sui requisiti [NAT](../expressroute/expressroute-nat.md) per ExpressRoute.

Altre informazioni su [ExpressRoute](../expressroute/expressroute-introduction.md), sui rispettivi [prezzi](https://azure.microsoft.com/pricing/details/expressroute) e sui [provider di connettività](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Ulteriori considerazioni
* Alcune delle opzioni indicate prevedono diversi limiti massimi che possono essere supportati per connessioni alle reti virtuali, connessioni a gateway e altri criteri. È consigliabile esaminare i [limiti di rete](../azure-subscription-service-limits.md#networking-limits) di Azure per ottenere informazioni su eventuali impatti sui tipi di connettività selezionati per l'uso.
* Se si prevede di connettere un gateway da una connessione VPN da sito a sito alla stessa rete virtuale di un gateway ExpressRoute, è necessario acquisire prima di tutto familiarità con i vincoli rilevanti. Per altri dettagli, vedere l'articolo [Configurare connessioni coesistenti da sito a sito ed ExpressRoute](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations).

## <a name="next-steps"></a>Passaggi successivi
Le risorse seguenti mostrano come implementare i tipi di connessione illustrati in questo articolo.

* [Implementare una connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Implementare una connessione da sito a sito](guidance-hybrid-network-vpn.md)
* [Implementare una connessione privata dedicata](guidance-hybrid-network-expressroute.md)
* [Implementare una connessione privata dedicata con una connessione da sito a sito per disponibilità elevata](guidance-hybrid-network-expressroute-vpn-failover.md)
