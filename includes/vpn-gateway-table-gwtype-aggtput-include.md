---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/12/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b698dd03473dd3cb708c47c6554869eebba48bf9
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/13/2018
ms.locfileid: "51597620"
---
|**SKU**   | **Tunnel S2S/<br>rete virtuale-rete virtuale** | **P2S<br> Connessioni SSTP** | **P2S<br> Connessioni IKEv2** | **Benchmark<br>velocità effettiva aggregata** | **BGP** |
|---       | ---        | ---       | ---            | ---       | --- |
|**Basic** | Max. 10    | Max. 128  | Non supportato  | 100 Mbps  | Non supportato|
|**VpnGw1**| Max. 30*   | Max. 128  | Max. 250       | 650 Mbps  | Supportato |
|**VpnGw2**| Max. 30*   | Max. 128  | Max. 500       | 1 Gbps    | Supportato |
|**VpnGw3**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbps | Supportato |


(*) Usare la [WAN virtuale](../articles/virtual-wan/virtual-wan-about.md) se servono più di 30 tunnel VPN S2S.

* Il benchmark della velocità effettiva aggregata per un gateway VPN è S2S + P2S combinati. **Se si hanno molte connessioni P2S, si può avere un impatto negativo su una connessione S2S a causa delle limitazioni relative alla velocità effettiva.** Il benchmark della velocità effettiva aggregata si basa sulle misurazioni di più tunnel aggregati tramite un singolo gateway. Non è una velocità effettiva garantita, poiché può variare anche in funzione delle condizioni del traffico Internet e dei comportamenti dell'applicazione.

* Questi limiti di connessione sono separati. Ad esempio, è possibile avere 128 connessioni SSTP e anche 250 connessioni IKEv2 in uno SKU VpnGw1.

* Le informazioni sui prezzi sono disponibili nella pagina [Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway) .

* Le informazioni sul contratto di servizio sono disponibili nella pagina [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) (Contratto di servizio).

* VpnGw1, VpnGw2 e VpnGw3 sono supportati solo per gateway VPN che usano il modello di distribuzione Resource Manager.
