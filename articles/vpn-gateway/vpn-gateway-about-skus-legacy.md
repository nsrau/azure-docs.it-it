---
title: SKU del gateway di rete virtuale di Azure legacy| Microsoft Docs
description: SKU del gateway di rete virtuale precedenti.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/31/2017
ms.author: cherylmc
ms.openlocfilehash: d5127c7fa512bad49817fa4c8edf3a16ca2f7d60
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/01/2017
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Utilizzo degli SKU del gateway di rete virtuale (SKU di versione precedente)

Questo articolo contiene informazioni sugli SKU del gateway di rete virtuale (versione precedente). Gli SKU di versione precedente continuano a funzionare in entrambi i modelli di distribuzione per i gateway VPN che sono già stati creati. I gateway VPN classici continuano a usare gli SKU di versione precedente, sia per i gateway esistenti sia per i nuovi gateway. Quando si creano nuovi gateway VPN di Resource Manager, usare i nuovi SKU del gateway. Per informazioni sui nuovi SKU, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKU del gateway

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Velocità effettiva aggregata stimata per SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Configurazioni supportate per tipo di VPN e SKU

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Ridimensionare un gateway (modificare uno SKU del gateway)

È possibile ridimensionare uno SKU del gateway solo all'interno della stessa famiglia di SKU. Se si ha uno SKU Standard, ad esempio, è possibile ridimensionarlo in uno SKU HighPerformance. Non è possibile invece ridimensionare gateway VPN tra gli SKU di versione precedente e le nuove famiglie di SKU. Non è possibile, ad esempio, passare da uno SKU Standard a uno SKU VpnGw2.

>[!IMPORTANT]
>Quando si ridimensiona un gateway, è necessario prevedere 20-30 minuti di inattività per tale gateway durante l'operazione di ridimensionamento.
>
>

Per ridimensionare uno SKU del gateway per il modello di distribuzione classica, usare il comando seguente:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Per ridimensionare uno SKU del gateway per il modello di distribuzione di Resource Manager, usare il comando seguente:

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

## <a name="migrate"></a>Eseguire la migrazione ai nuovi SKU del gateway

Se si sta usando il modello di distribuzione di Resource Manager, è possibile eseguire la migrazione ai nuovi SKU del gateway. Se si sta usando il modello di distribuzione classica, non è possibile eseguire la migrazione ai nuovi SKU ed è necessario invece continuare a usare gli SKU di versione precedente.

[!INCLUDE [Migrate SKU](../../includes/vpn-gateway-migrate-legacy-sku-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui nuovi SKU del gateway, vedere [SKU del gateway](vpn-gateway-about-vpngateways.md#gwsku).

Per altre informazioni sulle impostazioni di configurazione, vedere [Informazioni sulle impostazioni di configurazione del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).
