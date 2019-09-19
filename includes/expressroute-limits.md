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
ms.openlocfilehash: 45d34297bf37a6e46bc57e95ff49def49051e32e
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "67622105"
---
| Risorsa | Limite predefinito/massimo |
| --- | --- |
| Circuiti ExpressRoute per ogni sottoscrizione |10 |
| Circuiti ExpressRoute per area per ogni sottoscrizione, con Azure Resource Manager |10 |
| Numero massimo di route annunciate al peering privato di Azure con ExpressRoute standard |4\.000 |
| Numero massimo di route annunciate al peering privato di Azure con il componente aggiuntivo ExpressRoute Premium |10,000 |
| Numero massimo di route annunciate dal peering privato di Azure dallo spazio di indirizzi della VNet per una connessione ExpressRoute |200 |
| Numero massimo di route annunciate al peering Microsoft con ExpressRoute standard |200 |
| Numero massimo di route annunciate al peering Microsoft con il componente aggiuntivo ExpressRoute Premium |200 |
| Numero massimo di circuiti ExpressRoute collegati alla stessa rete virtuale nella stessa posizione di peering |4 |
| Numero massimo di circuiti ExpressRoute collegati alla stessa rete virtuale in diverse posizioni di peering |4 |
| Numero di collegamenti alla rete virtuale consentiti per ogni circuito ExpressRoute |Vedere il [numero di reti virtuali per ogni tabella del circuito ExpressRoute](#vnetpercircuit) .  |

#### <a name="vnetpercircuit"></a>Numero di reti virtuali per ogni circuito ExpressRoute
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
