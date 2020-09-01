---
title: includere file
description: File di inclusione
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 07/25/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 146a257a9b8f31e79283d71ede1e1c5c0355765c
ms.sourcegitcommit: d7352c07708180a9293e8a0e7020b9dd3dd153ce
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/30/2020
ms.locfileid: "89147772"
---
| Risorsa | Limite |
| --- | --- |
| Circuiti ExpressRoute per ogni sottoscrizione |10 |
| Circuiti ExpressRoute per ogni area per sottoscrizione con Azure Resource Manager |10 |
| Numero massimo di route annunciate al peering privato di Azure con ExpressRoute Standard |4.000 |
| Numero massimo di route annunciate al peering privato di Azure con il componente aggiuntivo ExpressRoute Premium |10,000 |
| Numero massimo di route annunciate dal peering privato di Azure dallo spazio indirizzi della rete virtuale per una connessione ExpressRoute |200 |
| Numero massimo di route annunciate al peering Microsoft con ExpressRoute Standard |200 |
| Numero massimo di route annunciate al peering Microsoft con il componente aggiuntivo ExpressRoute Premium |200 |
| Numero massimo di circuiti ExpressRoute collegati a un gateway Standard Performance/ErGw1AZ |4 | 
| Numero massimo di circuiti ExpressRoute collegati a un gateway High Performance/ErGw2AZ |8 | 
| Numero massimo di circuiti ExpressRoute collegati a un gateway Ultra Performance/ErGw3AZ |16 | 
| Numero massimo di circuiti ExpressRoute collegati alla stessa rete virtuale in diverse posizioni di peering (tutti gli SKU) |4 |
| Numero di collegamenti alla rete virtuale consentiti per ogni circuito ExpressRoute |Vedere la tabella [Numero di reti virtuali per circuito ExpressRoute](#vnetpercircuit).  |

#### <a name="number-of-virtual-networks-per-expressroute-circuit"></a><a name="vnetpercircuit"></a>Numero di reti virtuali per circuito ExpressRoute
| **Dimensioni del circuito** | **Numero di collegamenti di rete virtuale per Standard** | **Numero di collegamenti di rete virtuale con il componente aggiuntivo Premium** |
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

**Solo ExpressRoute Direct a 100 Gbps*

> [!NOTE]
> Le connessioni di tipo Copertura globale vengono conteggiate rispetto al limite di connessioni di rete virtuale per ogni circuito ExpressRoute. Un circuito Premium a 10 Gbps, ad esempio, consentirebbe 5 connessioni di tipo Copertura globale e 95 connessioni ai gateway ExpressRoute oppure 95 connessioni di tipo Copertura globale connessioni e 5 connessioni ai gateway ExpressRoute o qualsiasi altra combinazione fino al limite di 100 connessioni per il circuito.
