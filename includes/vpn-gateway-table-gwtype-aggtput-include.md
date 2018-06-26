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
ms.openlocfilehash: bc42697f756ec75d9a8f2c20c99b28b2f7886ca1
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/01/2018
ms.locfileid: "34670276"
---
|**SKU**   | **Tunnel S2S/<br>rete virtuale-rete virtuale** | **Connessioni<br>P2S** | **Benchmark<br>velocità effettiva aggregata** |
|---       | ---                             | ---                    | ---                         |
|**VpnGw1**| Max. 30                         | Max. 128*              | 650 Mbps                    |
|**VpnGw2**| Max. 30                         | Max. 128*              | 1 Gbps                      |
|**VpnGw3**| Max. 30                         | Max. 128*              | 1,25 Gbps                   |
|**Basic** | Max. 10                         | Max. 128               | 100 Mbps                    | 

*Contattare l'assistenza se sono necessarie connessioni aggiuntive
- Il benchmark della velocità effettiva aggregata si basa sulle misurazioni di più tunnel aggregati tramite un singolo gateway. Non è una velocità effettiva garantita, poiché può variare anche in funzione delle condizioni del traffico Internet e dei comportamenti dell'applicazione.

- Le informazioni sui prezzi sono disponibili nella pagina [Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway) .

- Le informazioni sul contratto di servizio sono disponibili nella pagina [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) (Contratto di servizio).

- VpnGw1, VpnGw2 e VpnGw3 sono supportati solo per gateway VPN che usano il modello di distribuzione Resource Manager.