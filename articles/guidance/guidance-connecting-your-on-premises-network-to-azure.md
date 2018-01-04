---
title: Connessione di rete locale a Azure | Documenti Microsoft
description: Viene illustrato e confronta i diversi metodi disponibili per la connessione a servizi cloud Microsoft, ad esempio Azure, Office 365 e Dynamics CRM Online.
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
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="connecting-your-on-premises-network-to-azure"></a>Connessione di rete locale a Azure
Microsoft fornisce diversi tipi di servizi cloud. Mentre è possibile connettersi a tutti i servizi sulla rete Internet pubblica, è anche possibile connettersi ad alcuni dei servizi tramite un tunnel di rete privata virtuale (VPN) tramite Internet o su una connessione diretta privata a Microsoft. In questo articolo consente di determinare quali opzioni di connettività più adatta a soddisfare le esigenze in base ai tipi di servizi cloud Microsoft che utilizzano. La maggior parte delle organizzazioni di utilizzare più tipi di connessione descritti di seguito.

## <a name="connecting-over-the-public-internet"></a>Connessione attraverso la rete Internet pubblica
Questo tipo di connessione fornisce l'accesso a servizi cloud Microsoft direttamente su Internet, come illustrato di seguito.

![Internet](./media/guidance-connecting-your-on-premises-network-to-azure/internet.png "Internet")

Questa connessione è in genere il primo tipo utilizzato per la connessione a servizi cloud Microsoft. Nella tabella seguente sono elencati i vantaggi e svantaggi di questo tipo di connessione.

| **Vantaggi** | **Considerazioni** |
| --- | --- |
| Non richiede alcuna modifica alla rete locale, purché tutti i dispositivi client hanno accesso illimitato a tutti gli indirizzi IP e le porte su Internet. |Se il traffico viene spesso crittografato tramite HTTPS, può essere intercettato in transito in quanto attraversano la rete Internet pubblica. |
| Connettersi a tutti i servizi cloud Microsoft esposti a Internet pubblico. |Latenza prevedibile in quanto la connessione attraversa Internet. |
| Utilizza la connessione Internet esistente. | |
| Non richiede la gestione dei dispositivi di connettività. | |

Questa connessione non ha connettività o i costi di larghezza di banda in quanto è utilizzare la connessione Internet esistente.

## <a name="connecting-with-a-point-to-site-connection"></a>Connessione con una connessione point-to-site
Questo tipo di connessione fornisce l'accesso ad alcuni servizi cloud Microsoft tramite un tunnel SSTP Secure Socket Tunneling Protocol () su Internet, come illustrato di seguito.

![P2S](./media/guidance-connecting-your-on-premises-network-to-azure/p2s.png "connessionePoint-to-site")

La connessione viene effettuata tramite la connessione Internet esistente, ma richiede l'uso di un Gateway VPN di Azure. Nella tabella seguente sono elencati i vantaggi e svantaggi di questo tipo di connessione.

| **Vantaggi** | **Considerazioni** |
| --- | --- |
| Non richiede alcuna modifica alla rete locale, purché tutti i dispositivi client hanno accesso illimitato a tutti gli indirizzi IP e le porte su Internet. |Se il traffico viene crittografato tramite IPSec, può essere intercettato in transito in quanto attraversano la rete Internet pubblica. |
| Utilizza la connessione Internet esistente. |Latenza prevedibile in quanto la connessione attraversa Internet. |
| Velocità effettiva fino a 200 Mb/s per il gateway. |È necessario creare e gestire le connessioni separate tra ogni dispositivo nella rete locale e ogni gateway che deve connettersi a ogni dispositivo. |
| Consente di connettersi a servizi di Azure che possono essere connesso a un reti virtuali di Azure (VNet), ad esempio macchine virtuali di Azure e servizi Cloud di Azure. |Richiede la minima amministrazione in corso di un Gateway VPN di Azure. |
| Non può essere utilizzato per connettersi a Microsoft Office 365 o di Dynamics CRM Online. | |
| Non può essere utilizzato per connettersi a servizi di Azure che non possono essere connessa a una rete virtuale. | |

Altre informazioni, vedere il [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) servizio, il relativo [prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway)e il trasferimento di dati in uscita [prezzi](https://azure.microsoft.com/pricing/details/data-transfers).

## <a name="connecting-with-a-site-to-site-connection"></a>Connessione con una connessione site-to-site
Questo tipo di connessione fornisce l'accesso ad alcuni servizi cloud Microsoft tramite un tunnel IPSec tramite Internet, come illustrato di seguito.

![S2S](./media/guidance-connecting-your-on-premises-network-to-azure/s2s.png "connessione Site-to-site")

La connessione viene effettuata tramite la connessione Internet esistente, ma richiede l'uso di un Gateway VPN di Azure con i relativi prezzi associati e il prezzo di trasferimento dati in uscita. Nella tabella seguente sono elencati i vantaggi e svantaggi di questo tipo di connessione.

| **Vantaggi** | **Considerazioni** |
| --- | --- |
| Tutti i dispositivi nella rete locale possono comunicare con servizi di Azure connessi a una rete virtuale, pertanto non è necessario configurare singole connessioni per ogni dispositivo. |Se il traffico viene crittografato tramite IPSec, può essere intercettato in transito in quanto attraversano la rete Internet pubblica. |
| Utilizza la connessione Internet esistente. |Latenza prevedibile in quanto la connessione attraversa Internet. |
| Può essere utilizzato per connettersi ai servizi Azure che possono essere connesso a una rete virtuale, ad esempio macchine virtuali e servizi Cloud. |È necessario configurare e gestire un convalidato dispositivo VPN * locale. |
| Velocità effettiva fino a 200 Mb/s per il gateway. |Richiede la minima amministrazione in corso di un Gateway VPN di Azure. |
| Forzare il traffico in uscita avviato da cloud di macchine virtuali attraverso la rete locale per l'ispezione e la registrazione utilizzando le route definite dall'utente o il protocollo BGP (Border Gateway) * *. |Non può essere utilizzato per connettersi a Microsoft Office 365 o di Dynamics CRM Online. |
| Non può essere utilizzato per connettersi a servizi di Azure che non possono essere connessa a una rete virtuale. | |
| Se si utilizzano servizi che avviano connessioni al dispositivi locali e i criteri di sicurezza richiedono, potrebbe essere un firewall tra la rete locale e Azure. | |

* * Consente di visualizzare un elenco di [dispositivi VPN convalidati](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable).
* * * Ulteriori informazioni sull'utilizzo [le route definite dall'utente](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md) o [BGP](../vpn-gateway/vpn-gateway-bgp-overview.md) per forzare il routing di reti virtuali di Azure a un dispositivo locale.

## <a name="connecting-with-a-dedicated-private-connection"></a>Connessione con una connessione privata dedicata
Questo tipo di connessione fornisce l'accesso a tutti i servizi cloud di Microsoft su una connessione privata dedicata a Microsoft che non passano attraverso Internet, come illustrato di seguito.

![ER](./media/guidance-connecting-your-on-premises-network-to-azure/er.png "connessione ExpressRoute")

La connessione richiede l'utilizzo del servizio di ExpressRoute e una connessione a un provider di connettività. Nella tabella seguente sono elencati i vantaggi e svantaggi di questo tipo di connessione.

| **Vantaggi** | **Considerazioni** |
| --- | --- |
| Poiché viene utilizzata una connessione dedicata tramite un provider di servizi, il traffico non è possibile intercettare in transito sulla rete Internet pubblica. |Richiede la gestione di router locale. |
| Larghezza di banda fino a 10 Gb/s per il circuito ExpressRoute e la velocità effettiva fino a 2 Gb/s per ogni gateway. |Richiede una connessione dedicata a un provider di connettività. |
| Latenza prevedibile perché è una connessione dedicata a Microsoft che non passano attraverso Internet. |Potrebbero essere necessari minimo amministrazione in corso di uno o più gateway VPN di Azure (se la connessione del circuito per le reti virtuali). |
| Non richiede la comunicazione crittografata, anche se è possibile crittografare il traffico, se necessario. |Se si usa servizi cloud che avviano connessioni al dispositivi locali, potrebbe essere un firewall tra la rete locale e Azure. |
| Possono essere connesse direttamente a tutti i servizi cloud Microsoft, con alcune eccezioni *. |Richiede il servizio NAT (Network address translation) di indirizzi IP locale immettendo i data center di Microsoft per i servizi che non possono essere connessa a VNet.* * |
| Forzare il traffico in uscita avviato da cloud di macchine virtuali attraverso la rete locale per l'ispezione e la registrazione utilizzando il protocollo BGP. | |

* * Consente di visualizzare un [elenco dei servizi](../expressroute/expressroute-faqs.md#supported-services) che non possono essere utilizzati con ExpressRoute. La sottoscrizione di Azure deve essere approvata per connettersi a Office 365.  Vedere il [Azure ExpressRoute per Office 365](https://support.office.com/article/Azure-ExpressRoute-for-Office-365-6d2534a2-c19c-4a99-be5e-33a0cee5d3bd?ui=en-US&rs=en-US&ad=US&fromAR=1) articolo per informazioni dettagliate.
* * * Ulteriori informazioni su ExpressRoute [NAT](../expressroute/expressroute-nat.md) requisiti.

Altre informazioni, vedere [ExpressRoute](../expressroute/expressroute-introduction.md), associato [prezzi](https://azure.microsoft.com/pricing/details/expressroute), e [i provider di connettività](../expressroute/expressroute-locations.md#locations).

## <a name="additional-considerations"></a>Ulteriori considerazioni
* Molte delle opzioni sopra presentano vari limiti massimi che per le connessioni, connessioni gateway e ad altri criteri di rete virtuale possono supportare. Si consiglia di esaminare Azure [limiti di rete](../azure-subscription-service-limits.md#networking-limits) comprendere se uno di essi influenzati si sceglie di utilizzare i tipi di connettività.
* Se si intende un gateway di connessione da una connessione VPN da sito a sito per la stessa rete virtuale come gateway ExpressRoute, è consigliabile acquisire familiarità con vincoli importanti prima di tutto. Vedere il [Site-to-Site ed ExpressRoute di configurare connessioni coesistenti](../expressroute/expressroute-howto-coexist-resource-manager.md#limits-and-limitations) per ulteriori dettagli.

## <a name="next-steps"></a>Passaggi successivi
Le risorse seguenti viene illustrato come implementare i tipi di connessione contemplati in questo articolo.

* [Implementare una connessione point-to-site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)
* [Implementare una connessione site-to-site](guidance-hybrid-network-vpn.md)
* [Implementare una connessione privata dedicata](guidance-hybrid-network-expressroute.md)
* [Implementare una connessione privata dedicata con una connessione da sito a sito per la disponibilità elevata](guidance-hybrid-network-expressroute-vpn-failover.md)
