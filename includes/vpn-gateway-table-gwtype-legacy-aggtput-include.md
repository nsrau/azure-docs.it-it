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
ms.openlocfilehash: 9734859c0bf22201c146e5d8a220f3146f6051c4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66159279"
---
La tabella seguente illustra i tipi di gateway e la velocità effettiva aggregata stimata per SKU di gateway. La tabella è valida per i modelli di distribuzione classica e di Gestione risorse. 

I prezzi variano a seconda dello SKU del gateway. Per altre informazioni, vedere [Gateway VPN Prezzi](https://azure.microsoft.com/pricing/details/vpn-gateway).

Si noti che lo SKU di gateway UltraPerformance non è rappresentato in questa tabella. Per informazioni sullo SKU UltraPerformance, vedere la documentazione relativa a [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

|  | **Velocità effettiva del gateway VPN (1)** | **Tunnel IPsec massimi del gateway VPN (2)** | **Velocità effettiva del gateway di ExpressRoute** | **Coesistenza gateway VPN ed ExpressRoute** |
| --- | --- | --- | --- | --- |
| **SKU Basic (3)(5)(6)** |100 Mbps |10 |500 Mbps (6) |No |
| **SKU Standard (4)(5)** |100 Mbps |10 |1000 Mbps |Yes |
| **SKU con prestazioni elevate (4)** |200 Mbps |30 |2000 Mbps |Yes |


(1) La velocità effettiva della VPN è una stima approssimativa basata sulle misurazioni tra reti virtuali nella stessa area di Azure. Non è una velocità effettiva garantita per le connessioni cross-premise tramite Internet. È la misura massima possibile per la velocità effettiva.

(2) Il numero di tunnel si riferisce alle VPN RouteBased. Una VPN PolicyBased può supportare solo un tunnel VPN da sito a sito.

(3) BGP non è supportato nello SKU di livello Basic.

(4) Le VPN basate su criteri non sono supportate in questo SKU. Sono supportate solo nello SKU di livello Basic.

(5) Le connessioni del gateway VPN S2S attivo/attivo non sono supportate per questo SKU. La modalità attivo/attivo è supportata solo nello SKU HighPerformance.

(6) Lo SKU Basic è deprecato per l'uso con ExpressRoute.
