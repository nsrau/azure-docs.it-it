---
title: File di inclusione
description: File di inclusione
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 06/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c025c431d826d3a2951a9eb5c09308695e172887
ms.sourcegitcommit: f013c433b18de2788bf09b98926c7136b15d36f1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/13/2019
ms.locfileid: "65551216"
---
| Resource | Limite predefinito o massimo |
| --- | --- |
| Circuiti ExpressRoute per ogni sottoscrizione |10 |
| Circuiti ExpressRoute per area per sottoscrizione, con Azure Resource Manager |10 |
| Numero massimo di route per il peering privato di Azure con ExpressRoute Standard |4.000 |
| Numero massimo di route per il peering privato di Azure con il componente aggiuntivo ExpressRoute Premium |10,000 |
| Numero massimo di route per il peering privato di Azure dallo spazio degli indirizzi della rete virtuale per una connessione ExpressRoute |200 | 
| Numero massimo di route per il peering di Microsoft Azure con ExpressRoute Standard |200 |
| Numero massimo di route per il peering di Microsoft Azure con il componente aggiuntivo ExpressRoute Premium |200 |
| Numero massimo di circuiti ExpressRoute collegati alla stessa rete virtuale nella stessa località di peering |4 |
| Numero massimo di circuiti ExpressRoute collegati alla stessa rete virtuale in diverse posizioni di peering |4 |
| Numero di collegamenti alla rete virtuale consentiti per ogni circuito ExpressRoute |Vedere la tabella seguente. |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Numero di reti virtuali per ogni circuito ExpressRoute
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
