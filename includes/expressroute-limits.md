---
title: File di inclusione
description: File di inclusione
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 05/28/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 9f7f9b1504533e0ec503e97c086c380da71671a0
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299701"
---
| Resource | Limite predefinito o massimo |
| --- | --- |
| Circuiti ExpressRoute per ogni sottoscrizione |10 |
| Circuiti ExpressRoute per area per sottoscrizione, con Azure Resource Manager |10 |
| Numero massimo di route annunciati su peering privato di Azure con ExpressRoute Standard |4.000 |
| Numero massimo di route annunciati su peering privato di Azure con il componente aggiuntivo ExpressRoute Premium |10,000 |
| Numero massimo di route annunciate dal peering privato di Azure dallo spazio degli indirizzi della rete virtuale per una connessione ExpressRoute |200 |
| Numero massimo di route annunciati su peering Microsoft con ExpressRoute Standard |200 |
| Numero massimo di route annunciati a Microsoft il peering con il componente aggiuntivo ExpressRoute Premium |200 |
| Numero massimo di circuiti ExpressRoute collegati alla stessa rete virtuale nella stessa località di peering |4 |
| Numero massimo di circuiti ExpressRoute collegati alla stessa rete virtuale in diverse posizioni di peering |4 |
| Numero di collegamenti alla rete virtuale consentiti per ogni circuito ExpressRoute |Vedere le [numero di reti virtuali per ogni circuito ExpressRoute](#vnetpercircuit) tabella.  |

#### <a name="vnetpercircuit"></a> Numero di reti virtuali per ogni circuito ExpressRoute
| **Dimensioni circuito** | **Numero di collegamenti di rete virtuale per il livello Standard** | **Numero di collegamenti di rete virtuale con il componente aggiuntivo Premium** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |
| 40 Gbps* |10 |100 |
| 100 Gbps* |10 |100 |

* Solo Direct ExpressRoute
