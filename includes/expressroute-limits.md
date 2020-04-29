---
title: includere il file
description: File di inclusione
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d1402bcc0c46003429e1809e8d09e9662218cd82
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80335104"
---
| Risorsa | Limite |
| --- | --- |
| Circuiti ExpressRoute per ogni sottoscrizione |10 |
| Circuiti ExpressRoute per area per ogni sottoscrizione, con Azure Resource Manager |10 |
| Numero massimo di route annunciate al peering privato di Azure con ExpressRoute standard |4.000 |
| Numero massimo di route annunciate al peering privato di Azure con il componente aggiuntivo ExpressRoute Premium |10,000 |
| Numero massimo di route annunciate dal peering privato di Azure dallo spazio di indirizzi della VNet per una connessione ExpressRoute |200 |
| Numero massimo di route annunciate al peering Microsoft con ExpressRoute standard |200 |
| Numero massimo di route annunciate al peering Microsoft con il componente aggiuntivo ExpressRoute Premium |200 |
| Numero massimo di circuiti ExpressRoute collegati alla stessa rete virtuale nella stessa posizione di peering |4 |
| Numero massimo di circuiti ExpressRoute collegati alla stessa rete virtuale in diverse posizioni di peering |4 |
| Numero di collegamenti alla rete virtuale consentiti per ogni circuito ExpressRoute |Vedere il [numero di reti virtuali per ogni tabella del circuito ExpressRoute](#vnetpercircuit) .  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>Numero di reti virtuali per ogni circuito ExpressRoute
| **Dimensioni del circuito** | **Numero di collegamenti di rete virtuale per standard** | **Numero di collegamenti alla rete virtuale con componente aggiuntivo Premium** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps * |10 |100 |
| 100 Gbps * |10 |100 |

**Solo ExpressRoute a 100 Gbps*

> [!NOTE]
> Le connessioni Copertura globale vengono conteggiate rispetto al limite di connessioni di rete virtuale per ogni circuito ExpressRoute. Un circuito Premium da 10 Gbps, ad esempio, consentirebbe 5 connessioni Copertura globale e 95 connessioni ai gateway ExpressRoute o 95 Copertura globale connessioni e 5 connessioni ai gateway ExpressRoute o qualsiasi altra combinazione fino al limite di 100 connessioni per il circuito.
