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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "73495772"
---
È possibile `Resize-AzVirtualNetworkGateway` utilizzare il cmdlet PowerShell per aggiornare o eseguire il downgrade di uno SKU di generazione 1 o generazione2 (tutti gli SKU VpnGw possono essere ridimensionati ad eccezione degli SKU di base). Se si usa lo SKU di gateway Basic [seguire in alternativa queste istruzioni](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) per ridimensionare il gateway.

L'esempio di PowerShell seguente illustra uno SKU del gateway che viene ridimensionato come VpnGw2.

```azurepowershell-interactive
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

È anche possibile ridimensionare un gateway nel portale di Azure, passando alla pagina **Configurazione** per il gateway di rete virtuale e selezionando uno SKU diverso dall'elenco a discesa.
