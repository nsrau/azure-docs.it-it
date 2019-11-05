---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4842c55b2b1fd23f4d6b7996ccf02e7141504836
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73495772"
---
È possibile usare il cmdlet `Resize-AzVirtualNetworkGateway` PowerShell per aggiornare o effettuare il downgrade di uno SKU Generation1 o Generation2 (tutti gli SKU VpnGw possono essere ridimensionati ad eccezione degli SKU Basic). Se si usa lo SKU di gateway Basic [seguire in alternativa queste istruzioni](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) per ridimensionare il gateway.

L'esempio di PowerShell seguente illustra uno SKU del gateway che viene ridimensionato come VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

È anche possibile ridimensionare un gateway nel portale di Azure, passando alla pagina **Configurazione** per il gateway di rete virtuale e selezionando uno SKU diverso dall'elenco a discesa.
