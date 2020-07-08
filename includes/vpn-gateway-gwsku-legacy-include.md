---
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: cherylmc
ms.openlocfilehash: 622a2f69c2e7b82f2df10d6120ee2dd466961915
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "77211393"
---
Di seguito sono elencati gli SKU del gateway VPN legacy (precedenti):

* Impostazione predefinita (base)
* Standard
* HighPerformance

Il gateway VPN non usa lo SKU UltraPerformance. Per informazioni sullo SKU UltraPerformance, vedere la documentazione relativa a [ExpressRoute](../articles/expressroute/expressroute-about-virtual-network-gateways.md).

Quando si usano SKU legacy, tenere presente quanto segue:

* Per usare un tipo di VPN PolicyBased, è necessario usare lo SKU di livello Basic. Le VPN basate su criteri, precedentemente denominate routing statico, non sono supportate negli altri SKU.
* BGP non è supportato nello SKU di livello Basic.
* Le configurazioni con coesistenza di gateway VPN ed ExpressRoute non sono supportate nello SKU di livello Basic.
* Le connessioni del gateway VPN S2S attivo/attivo possono essere configurate solo nello SKU HighPerformance.