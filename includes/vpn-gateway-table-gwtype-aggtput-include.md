---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 03af5efcd4a37203a82db503f8bc602b33de734d
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226601"
---
|**SKU**   | **Tunnel S2S/<br>rete virtuale-rete virtuale** | **P2S<br> Connessioni SSTP** | **P2S<br> Connessioni IKEv2** | **Benchmark<br>velocità effettiva aggregata** |
|---       | ---        | ---       | ---            | ---       |
|**VpnGw1**| Max. 30*   | Max. 128  | Max. 250       | 650 Mbps  |
|**VpnGw2**| Max. 30*   | Max. 128  | Max. 500       | 1 Gbps    |
|**VpnGw3**| Max. 30*   | Max. 128  | Max. 1000      | 1,25 Gbps |
|**Basic** | Max. 10    | Max. 128  | Non supportato  | 100 Mbps  | 

(*) Usare la [WAN virtuale](../articles/virtual-wan/virtual-wan-about.md) se servono più di 30 tunnel VPN S2S.

* Il benchmark della velocità effettiva aggregata per un gateway VPN è S2S + P2S combinati. **Se si hanno molte connessioni P2S, si può avere un impatto negativo su una connessione S2S a causa delle limitazioni relative alla velocità effettiva.** Il benchmark della velocità effettiva aggregata si basa sulle misurazioni di più tunnel aggregati tramite un singolo gateway. Non è una velocità effettiva garantita, poiché può variare anche in funzione delle condizioni del traffico Internet e dei comportamenti dell'applicazione.

* Questi limiti di connessione sono separati. Ad esempio, è possibile avere 128 connessioni SSTP e anche 250 connessioni IKEv2 in uno SKU VpnGw1.

* Le informazioni sui prezzi sono disponibili nella pagina [Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway) .

* Le informazioni sul contratto di servizio sono disponibili nella pagina [SLA](https://azure.microsoft.com/support/legal/sla/vpn-gateway/) (Contratto di servizio).

* VpnGw1, VpnGw2 e VpnGw3 sono supportati solo per gateway VPN che usano il modello di distribuzione Resource Manager.
