---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c9457e51858d4a073d8baffdd435c8100d95d566
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/23/2018
---
|**SKU**   | **Tunnel S2S/<br>rete virtuale-rete virtuale** | **Connessioni<br>P2S** | **Benchmark<br>velocità effettiva aggregata** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Max. 30                         | Max. 128               | 650 Mbps                    |
|**VpnGw2**| Max. 30                         | Max. 128               | 1 Gbps                      |
|**VpnGw3**| Max. 30                         | Max. 128               | 1,25 Gbps                   |
|**Basic** | Max. 10                         | Max. 128               | 100 Mbps                    | 
|          |                                 |                        |                             | 

- Il benchmark della velocità effettiva aggregata si basa sulle misurazioni di più tunnel aggregati tramite un singolo gateway. Non è una velocità effettiva garantita, poiché può variare anche in funzione delle condizioni del traffico Internet e dei comportamenti dell'applicazione.

- Le informazioni sui prezzi sono disponibili nella pagina [Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway) .

- Le informazioni sul contratto di servizio sono disponibili nella pagina [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) (Contratto di servizio).

- VpnGw1, VpnGw2 e VpnGw3 sono supportati solo per gateway VPN che usano il modello di distribuzione Resource Manager.