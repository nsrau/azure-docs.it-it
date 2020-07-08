---
title: SKU del gateway VPN di rete virtuale di Azure legacy
description: Come usare gli SKU legacy del gateway di rete virtuale, Basic, Standard e HighPerformance.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 08/15/2019
ms.author: cherylmc
ms.openlocfilehash: 9c5e6d5aca51bd560a46837ba47de86362665773
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "84687787"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Utilizzo degli SKU del gateway di rete virtuale (SKU di versione precedente)

Questo articolo contiene informazioni sugli SKU del gateway di rete virtuale (versione precedente). Gli SKU di versione precedente continuano a funzionare in entrambi i modelli di distribuzione per i gateway VPN che sono già stati creati. I gateway VPN classici continuano a usare gli SKU di versione precedente, sia per i gateway esistenti sia per i nuovi gateway. Quando si creano nuovi gateway VPN di Resource Manager, usare i nuovi SKU del gateway. Per informazioni sui nuovi SKU, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).

## <a name="gateway-skus"></a><a name="gwsku"></a>SKU del gateway

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

È possibile visualizzare i prezzi del gateway legacy nella sezione **gateway di rete virtuale** , disponibile nella [pagina dei prezzi di ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="estimated-aggregate-throughput-by-sku"></a><a name="agg"></a>Velocità effettiva aggregata stimata per SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="supported-configurations-by-sku-and-vpn-type"></a><a name="config"></a>Configurazioni supportate per tipo di VPN e SKU

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize-a-gateway"></a><a name="resize"></a>Ridimensionare un gateway

È possibile ridimensionare il gateway in uno SKU del gateway solo all'interno della stessa famiglia di SKU. Se si ha uno SKU Standard, ad esempio, è possibile ridimensionarlo in uno SKU HighPerformance. Non è tuttavia possibile ridimensionare il gateway VPN tra gli SKU precedenti e le nuove famiglie di SKU. Non è ad esempio possibile passare da uno SKU Standard a uno SKU VpnGw2 o da uno SKU Basic a VpnGw1.

### <a name="resource-manager"></a>Gestione risorse

Per ridimensionare un gateway per il modello di distribuzione Resource Manager con PowerShell, usare il comando seguente:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```

È anche possibile ridimensionare un gateway nel portale di Azure.

### <a name="classic"></a><a name="classicresize"></a>Classico

Per ridimensionare un gateway per il modello di distribuzione classica, è necessario usare i cmdlet di PowerShell per la gestione dei servizi. Usare il comando seguente:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="change-to-the-new-gateway-skus"></a><a name="change"></a>Passare ai nuovi SKU del gateway

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui nuovi SKU del gateway, vedere [SKU del gateway](vpn-gateway-about-vpngateways.md#gwsku).

Per altre informazioni sulle impostazioni di configurazione, vedere [Informazioni sulle impostazioni di configurazione del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).
