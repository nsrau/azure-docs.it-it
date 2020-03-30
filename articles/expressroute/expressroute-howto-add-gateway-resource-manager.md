---
title: 'Azure ExpressRoute: Add a gateway to a VNet: PowerShell'
description: Questo articolo illustra come aggiungere un gateway di rete virtuale a una rete virtuale di Resource Manager già creata per ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 6e6d79afa1126acb26cb8856c39ba486cce31a5e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74037419"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Configurare un gateway di rete virtuale per ExpressRoute usando PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classica: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Portale di Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Questo articolo consente di aggiungere, ridimensionare e rimuovere un gateway di rete virtuale per una rete virtuale già esistente. I passaggi per questa configurazione si applicano alle reti virtuali create usando il modello di distribuzione di Resource Manager per una configurazione ExpressRoute.The steps for this configuration apply to VNets that were created using the Resource Manager deployment model for an ExpressRoute configuration. Per altre informazioni, vedere [Informazioni sui gateway di rete virtuale per ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Prima di iniziare

### <a name="working-with-powershell"></a>Utilizzo di PowerShellWorking with PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Elenco di riferimento per la configurazione

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato il gateway VNet, è possibile collegare la rete virtuale a un circuito ExpressRoute. Vedere [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).
