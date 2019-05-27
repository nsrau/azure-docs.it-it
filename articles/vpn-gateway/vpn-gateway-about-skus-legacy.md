---
title: SKU legacy del gateway di rete virtuale di Azure | Microsoft Docs
description: Come usare gli SKU legacy del gateway di rete virtuale, Basic, Standard e HighPerformance.
services: vpn-gateway
documentationcenter: na
author: WenJason
manager: digimobile
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
origin.date: 03/20/2018
ms.date: 03/04/2019
ms.author: v-jay
ms.openlocfilehash: 5a9e3f63a484069bf8cd39f8a545d7c37f05c63c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "66159239"
---
# <a name="working-with-virtual-network-gateway-skus-legacy-skus"></a>Utilizzo degli SKU del gateway di rete virtuale (SKU di versione precedente)

Questo articolo contiene informazioni sugli SKU del gateway di rete virtuale (versione precedente). Gli SKU di versione precedente continuano a funzionare in entrambi i modelli di distribuzione per i gateway VPN che sono già stati creati. I gateway VPN classici continuano a usare gli SKU di versione precedente, sia per i gateway esistenti sia per i nuovi gateway. Quando si creano nuovi gateway VPN di Resource Manager, usare i nuovi SKU del gateway. Per informazioni sui nuovi SKU, vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).

## <a name="gwsku"></a>SKU del gateway

[!INCLUDE [Legacy gateway SKUs](../../includes/vpn-gateway-gwsku-legacy-include.md)]

## <a name="agg"></a>Velocità effettiva aggregata stimata per SKU

[!INCLUDE [Aggregated throughput by legacy SKU](../../includes/vpn-gateway-table-gwtype-legacy-aggtput-include.md)]

## <a name="config"></a>Configurazioni supportate per tipo di VPN e SKU

[!INCLUDE [Table requirements for old SKUs](../../includes/vpn-gateway-table-requirements-legacy-sku-include.md)]

## <a name="resize"></a>Ridimensionare un gateway

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

È possibile ridimensionare il gateway in uno SKU del gateway solo all'interno della stessa famiglia di SKU. Se si ha uno SKU Standard, ad esempio, è possibile ridimensionarlo in uno SKU HighPerformance. Non è tuttavia possibile ridimensionare il gateway VPN tra gli SKU precedenti e le nuove famiglie di SKU. Non è ad esempio possibile passare da uno SKU Standard a uno SKU VpnGw2 o da uno SKU Basic a VpnGw1.

Per ridimensionare un gateway per il modello di distribuzione classica, usare il comando seguente:

```powershell
Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

Per ridimensionare un gateway per il modello di distribuzione Resource Manager con PowerShell, usare il comando seguente:

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance
```
È anche possibile ridimensionare un gateway nel portale di Azure.

## <a name="change"></a>Passare ai nuovi SKU del gateway

[!INCLUDE [Change to the new SKUs](../../includes/vpn-gateway-gwsku-change-legacy-sku-include.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui nuovi SKU del gateway, vedere [SKU del gateway](vpn-gateway-about-vpngateways.md#gwsku).

Per altre informazioni sulle impostazioni di configurazione, vedere [Informazioni sulle impostazioni di configurazione del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).

<!--Update_Description: wording update-->