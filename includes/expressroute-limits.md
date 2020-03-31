---
title: File di inclusione
description: File di inclusione
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335104"
---
| Risorsa | Limite |
| --- | --- |
| Circuiti ExpressRoute per ogni sottoscrizione |10 |
| Circuiti ExpressRoute per area per sottoscrizione, con Azure Resource Manager |10 |
| Numero massimo di route annunciate al peering privato di Azure con ExpressRoute StandardMaximum number of routes advertised to Azure private peering with ExpressRoute Standard |4.000 |
| Numero massimo di route annunciate al peering privato di Azure con il componente aggiuntivo ExpressRoute PremiumMaximum number of routes advertised to Azure private peering with ExpressRoute Premium add-on |10,000 |
| Numero massimo di route annunciate dal peering privato di Azure dallo spazio di indirizzi della rete virtuale per una connessione ExpressRouteMaximum number of routes advertised from Azure private peering from the VNet address space for an ExpressRoute connection |200 |
| Numero massimo di route annunciate per il peering Microsoft con ExpressRoute StandardMaximum number of routes advertised to Microsoft peering with ExpressRoute Standard |200 |
| Numero massimo di route annunciate al peering Microsoft con il componente aggiuntivo ExpressRoute PremiumMaximum number of routes advertised to Microsoft peering with ExpressRoute Premium add-on |200 |
| Numero massimo di circuiti ExpressRoute collegati alla stessa rete virtuale nello stesso percorso di peeringMaximum number of ExpressRoute circuits linked to the same virtual network in the same peering location |4 |
| Numero massimo di circuiti ExpressRoute collegati alla stessa rete virtuale in diverse posizioni di peering |4 |
| Numero di collegamenti alla rete virtuale consentiti per ogni circuito ExpressRoute |Vedere la tabella [Numero di reti virtuali per percorso di circuito ExpressRoute.See the Number of virtual networks per ExpressRoute circuit table.](#vnetpercircuit)  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>Numero di reti virtuali per circuito ExpressRouteNumber of virtual networks per ExpressRoute circuit
| **Dimensioni del circuito** | **Numero di collegamenti di rete virtuale per StandardNumber of virtual network links for Standard** | **Numero di collegamenti di rete virtuale con il componente aggiuntivo Premium** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps |10 |100 |
| 100 Gbps |10 |100 |

**100 Gbps ExpressRoute Solo diretto*

> [!NOTE]
> Le connessioni di copertura globale vengono conteggiate rispetto al limite di connessioni di rete virtuale per circuito ExpressRoute.Global Reach connections count against the limit of virtual network connections per ExpressRoute Circuit. Ad esempio, un circuito Premium da 10 Gbps consentirebbe 5 connessioni Global Reach e 95 connessioni ai gateway ExpressRoute o 95 connessioni Global Reach e 5 connessioni ai gateway ExpressRoute o a qualsiasi altra combinazione fino al limite di 100 connessioni per il circuito.
