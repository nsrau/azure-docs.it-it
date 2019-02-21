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
ms.openlocfilehash: 081352a23e6a0d8f9e2daa77eca1f8ac85172ff6
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418229"
---
Se per gli SKU correnti (VpnGw1, VpnGw2 e VPNGW3) si vuole ridimensionare lo SKU di gateway per eseguire l'aggiornamento a uno SKU più potente, è possibile usare il cmdlet di PowerShell `Resize-AzVirtualNetworkGateway`. È anche possibile eseguire il downgrade delle dimensioni dello SKU del gateway usando questo cmdlet. Se si usa lo SKU di gateway Basic [seguire in alternativa queste istruzioni](../articles/vpn-gateway/vpn-gateway-about-skus-legacy.md#resize) per ridimensionare il gateway.

L'esempio di PowerShell seguente illustra uno SKU del gateway che viene ridimensionato come VpnGw2.

```powershell
$gw = Get-AzVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
Resize-AzVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku VpnGw2
```

È anche possibile ridimensionare un gateway nel portale di Azure, passando alla pagina **Configurazione** per il gateway di rete virtuale e selezionando uno SKU diverso dall'elenco a discesa.