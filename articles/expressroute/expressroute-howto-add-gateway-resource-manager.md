---
title: 'Azure ExpressRoute: aggiungere un gateway a una VNet: PowerShell'
description: Questo articolo illustra come aggiungere un gateway di rete virtuale a una rete virtuale di Resource Manager già creata per ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: how-to
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 58d2949a18bfbf2800ae6ab4ac74b02b05b3eb07
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84736391"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Configurare un gateway di rete virtuale per ExpressRoute usando PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classica: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [portale di Azure video](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Questo articolo consente di aggiungere, ridimensionare e rimuovere un gateway di rete virtuale per una rete virtuale già esistente. I passaggi per questa configurazione si applicano a reti virtuali creati usando il modello di distribuzione Gestione risorse per una configurazione ExpressRoute. Per altre informazioni, vedere [Informazioni sui gateway di rete virtuale per ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Prima di iniziare

### <a name="working-with-powershell"></a>Uso di PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Elenco dei riferimenti alla configurazione

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato il gateway VNet, è possibile collegare la rete virtuale a un circuito ExpressRoute. Vedere [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).
