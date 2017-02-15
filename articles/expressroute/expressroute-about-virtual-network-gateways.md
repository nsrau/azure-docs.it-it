---
title: Informazioni sui gateway di rete virtuale per ExpressRoute | Documentazione Microsoft
description: Informazioni sui gateway di rete virtuale per ExpressRoute.
services: expressroute
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager, azure-service-management
ms.assetid: 7e0d9658-bc00-45b0-848f-f7a6da648635
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/01/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a575929f2c42a00b6e6cacd86318253d20b6b51e


---
# <a name="about-virtual-network-gateways-for-expressroute"></a>Informazioni sui gateway di rete virtuale per ExpressRoute
Il gateway di rete virtuale viene usato per inviare il traffico di rete tra le reti virtuali di Azure e i percorsi locali. Quando si configura una connessione ExpressRoute, è necessario creare e configurare un gateway di rete virtuale e la connessione corrispondente.

Quando si crea un gateway di rete virtuale, si devono specificare alcune impostazioni. Una delle impostazioni necessarie indica se il gateway verrà usato per il traffico VPN sito a sito o ExpressRoute. Nel modello di distribuzione di Gestione risorse, l'impostazione è "-GatewayType".

Quando il traffico di rete viene inviato con una connessione privata dedicata, si usa il tipo di gateway 'ExpressRoute', detto appunto gateway ExpressRoute. Quando il traffico di rete viene inviato crittografato attraverso una rete Internet pubblica, si usa il gateway di tipo 'VPN', detto appunto gateway VPN. Le connessioni da sito a sito, da punto a sito e da rete virtuale a rete virtuale usano tutte un gateway VPN. 

Ogni rete virtuale può avere un solo gateway di rete virtuale per tipo di gateway. Ad esempio, è possibile configurare un gateway di rete virtuale che usa -GatewayType Vpn e una che usa -GatewayType ExpressRoute. L'articolo è incentrato sul gateway di rete virtuale per ExpressRoute.

## <a name="a-namegwskuagateway-skus"></a><a name="gwsku"></a>SKU del gateway
[!INCLUDE [expressroute-gwsku-include](../../includes/expressroute-gwsku-include.md)]

Se si vuole aggiornare il gateway a uno SKU più potente, nella maggior parte dei casi è possibile usare il cmdlet PowerShell 'Resize-AzureRmVirtualNetworkGateway'. Questa tecnica funziona per gli aggiornamenti agli SKU Standard e HighPerformance. Tuttavia, per eseguire l'aggiornamento per allo SKU UltraPerformance sarà necessario ricreare il gateway.

### <a name="a-nameaggthroughputaestimated-aggregate-throughput-by-gateway-sku"></a><a name="aggthroughput"></a>Velocità effettiva aggregata stimata per SKU del gateway
La tabella seguente illustra i tipi di gateway e la velocità effettiva aggregata stimata. La tabella è valida per entrambi i modelli di distribuzione classica e di Gestione risorse.

[!INCLUDE [expressroute-table-aggthroughput](../../includes/expressroute-table-aggtput-include.md)]

## <a name="a-nameresourcesarest-apis-and-powershell-cmdlets"></a><a name="resources"></a>API REST e cmdlet PowerShell
Per altre risorse tecniche e requisiti di sintassi specifici quando si usano le API REST e i cmdlet PowerShell per le configurazioni di gateway di rete virtuale, vedere le pagine seguenti:

| **Classico** | **Gestione risorse** |
| --- | --- |
| [PowerShell](https://msdn.microsoft.com/library/mt270335.aspx) |[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx) |
| [API REST](https://msdn.microsoft.com/library/jj154113.aspx) |[API REST](https://msdn.microsoft.com/library/mt163859.aspx) |

## <a name="next-steps"></a>Passaggi successivi
Per altre informazioni sulle configurazioni delle connessioni disponibili, vedere [Panoramica tecnica relativa a ExpressRoute](expressroute-introduction.md) . 




<!--HONumber=Nov16_HO3-->


