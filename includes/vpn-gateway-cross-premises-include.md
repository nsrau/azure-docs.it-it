---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 05/16/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 8c82e3d2c978a7980d02bc686346acdcb21dd591
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66425776"
---
|  | **Da punto a sito** | **Da sito a sito** | **ExpressRoute** |
| --- | --- | --- | --- |
| **Servizi supportati di Azure** |Servizi cloud e Macchine virtuali |Servizi cloud e Macchine virtuali |[Elenco dei servizi](../articles/expressroute/expressroute-faqs.md#supported-services) |
| **Larghezze di banda tipiche** |Basate sullo SKU del gateway |Aggregazione tipica < 1 Gbps |50 Mbps, 100 Mbps, 200 Mbps, 500 Mbps, 1 Gbps, 2 Gbps, 5 Gbps, 10 Gbps |
| **Protocolli supportati** |SSTP (Secure Sockets Tunneling Protocol), OpenVPN e IPsec |IPsec |Connessione diretta su VLAN, tecnologie VPN del provider (MPLS, VPLS, ecc.) |
| **Routing** |RouteBased (dinamico) |Sono supportati il routing PolicyBased (routing statico) e il routing RouteBased (VPN routing dinamico) |BGP |
| **Resilienza della connessione** |attiva-passiva |attiva-passiva o attiva-attiva |attiva-attiva |
| **Caso d'uso tipico** |Creazione di prototipi, scenari di sviluppo/test /laboratorio per servizi cloud e macchine virtuali |Scenari di sviluppo/test/laboratorio e carichi di lavoro di produzione su scala ridotta per servizi cloud e macchine virtuali |Accesso a tutti i servizi di Azure (elenco convalidato), carichi di lavoro aziendali e di importanza strategica, backup, Big Data, Azure come sito di ripristino di emergenza |
| **CONTRATTO DI SERVIZIO** |[CONTRATTO DI SERVIZIO](https://azure.microsoft.com/support/legal/sla/) |[CONTRATTO DI SERVIZIO](https://azure.microsoft.com/support/legal/sla/) |[Contratto di servizio](https://azure.microsoft.com/support/legal/sla/) |
| **Prezzi** |[Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway/) |[Prezzi](https://azure.microsoft.com/pricing/details/expressroute/) |
| **Documentazione tecnica** |[Documentazione del gateway VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentazione del gateway VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) |[Documentazione di ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) |
| **DOMANDE FREQUENTI** |[Domande frequenti sul gateway VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Domande frequenti sul gateway VPN](../articles/vpn-gateway/vpn-gateway-vpn-faq.md) |[Domande frequenti su ExpressRoute](../articles/expressroute/expressroute-faqs.md) |
