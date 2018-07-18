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
ms.openlocfilehash: c06e69dd9d1997500589659e936dc25ee01ed145
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "30196781"
---
Se per gli SKU correnti (VpnGw1, VpnGw2 e VPNGW3) si vuole ridimensionare lo SKU di gateway per eseguire l'aggiornamento a uno SKU più potente, è possibile usare il cmdlet di PowerShell `Resize-AzureRmVirtualNetworkGateway`. È anche possibile eseguire il downgrade delle dimensioni dello SKU del gateway usando questo cmdlet. Se si usa lo SKU di gateway Basic [seguire in alternativa queste istruzioni](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) per ridimensionare il gateway.

L'esempio di PowerShell seguente illustra uno SKU del gateway che viene ridimensionato come VpnGw2.

```powershell
$gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

È anche possibile ridimensionare un gateway nel portale di Azure, passando alla pagina **Configurazione** per il gateway di rete virtuale e selezionando uno SKU diverso dall'elenco a discesa.