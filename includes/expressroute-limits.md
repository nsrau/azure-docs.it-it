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
ms.openlocfilehash: 6e0d2b881e00acc6b1fccd3196187309fc36cba2
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2018
ms.locfileid: "38755713"
---
#### <a name="expressroute-limits"></a>Limiti di ExpressRoute
Le seguenti limitazioni si applicano alle risorse di ExpressRoute per ogni sottoscrizione.

| Risorsa | Limite predefinito |
| --- | --- |
| Circuiti ExpressRoute per ogni sottoscrizione |10 |
| Circuiti ExpressRoute per ogni area per sottoscrizione di ARM |10 |
| Numero massimo di route per il peering privato di Azure con ExpressRoute Standard |4.000 |
| Numero massimo di route per il peering privato di Azure con l’estensione ExpressRoute Premium |10.000 |
| Numero massimo di route per il peering pubblico di Azure con ExpressRoute Standard |200 |
| Numero massimo di route per il peering pubblico di Azure con il componente aggiuntivo ExpressRoute Premium |200 |
| Numero massimo di route per il peering di Microsoft Azure con ExpressRoute Standard |200 |
| Numero massimo di route per il peering di Microsoft Azure con il componente aggiuntivo ExpressRoute Premium |200 |
| Numero massimo di circuiti ExpressRoute collegati alla stessa rete virtuale in diverse posizioni di peering |4 |
| Numero di collegamenti alla rete virtuale consentiti per ogni circuito ExpressRoute |vedere la tabella seguente |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a>Numero di reti virtuali per circuito ExpressRoute
| **Dimensioni circuito** | **Numero di collegamenti VNet per il livello standard** | **Numero di collegamenti VNet con il componente aggiuntivo Premium** |
| --- | --- | --- |
| 50 Mbps |10 |20 |
| 100 Mbps |10 |25 |
| 200 Mbps |10 |25 |
| 500 Mbps |10 |40 |
| 1 Gbps |10 |50 |
| 2 Gbps |10 |60 |
| 5 Gbps |10 |75 |
| 10 Gbps |10 |100 |

