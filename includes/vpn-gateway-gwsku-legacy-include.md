---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: f2ee442d0d6fecf34449ad28f058615a1274bbea
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66159256"
---
Di seguito sono elencati gli SKU del gateway VPN legacy (precedenti):

* Di base
* Standard
* HighPerformance

Il gateway VPN non usa lo SKU UltraPerformance. Per informazioni sullo SKU UltraPerformance, vedere la documentazione relativa a [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Quando si usano SKU legacy, tenere presente quanto segue:

* Per usare un tipo di VPN PolicyBased, è necessario usare lo SKU di livello Basic. Le VPN basate su criteri, precedentemente denominate routing statico, non sono supportate negli altri SKU.
* BGP non è supportato nello SKU di livello Basic.
* Le configurazioni con coesistenza di gateway VPN ed ExpressRoute non sono supportate nello SKU di livello Basic.
* Le connessioni del gateway VPN S2S attivo/attivo possono essere configurate solo nello SKU HighPerformance.