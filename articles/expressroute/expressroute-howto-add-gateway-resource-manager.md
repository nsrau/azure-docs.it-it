---
title: 'Aggiungere un gateway di rete virtuale per ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Questo articolo illustra come aggiungere un gateway di rete virtuale a una rete virtuale di Resource Manager già creata per ExpressRoute.
services: expressroute
author: charwen
ms.service: expressroute
ms.topic: article
ms.date: 02/21/2019
ms.author: charwen
ms.custom: seodec18
ms.openlocfilehash: 3c91fd6140b460d29b33e7d9b1fabafbbcf99422
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57406219"
---
# <a name="configure-a-virtual-network-gateway-for-expressroute-using-powershell"></a>Configurare un gateway di rete virtuale per ExpressRoute usando PowerShell
> [!div class="op_single_selector"]
> * [Resource Manager - Portale di Azure](expressroute-howto-add-gateway-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-add-gateway-resource-manager.md)
> * [Classica: PowerShell](expressroute-howto-add-gateway-classic.md)
> * [Video - Portale di Azure](https://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-vpn-gateway-for-your-virtual-network)
> 
> 

Questo articolo consente di aggiungere, ridimensionare e rimuovere un gateway di rete virtuale per una rete virtuale già esistente. I passaggi di questa configurazione si applicano alle reti virtuali create usando il modello di distribuzione Resource Manager per una configurazione ExpressRoute. Per altre informazioni, vedere [Informazioni sui gateway di rete virtuale per ExpressRoute](expressroute-about-virtual-network-gateways.md).

## <a name="before-beginning"></a>Prima di iniziare

### <a name="working-with-powershell"></a>Utilizzo di PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[!INCLUDE [working with cloud shell](../../includes/expressroute-cloud-shell-powershell-about.md)]

### <a name="configuration-reference-list"></a>Elenco di riferimento configurazione

[!INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Dopo avere creato il gateway VNet, è possibile collegare la rete virtuale a un circuito ExpressRoute. Vedere [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md).