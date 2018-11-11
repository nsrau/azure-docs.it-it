---
title: File di inclusione
description: File di inclusione
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: include
ms.date: 11/05/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fab00e5281bb91bce10228b3bc2e9cfd503d5d5b
ms.sourcegitcommit: 1b186301dacfe6ad4aa028cfcd2975f35566d756
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2018
ms.locfileid: "51219818"
---
Prima di eseguire le attività seguenti è necessario creare una rete virtuale e una subnet del gateway.

> [!NOTE]
> Questi esempi non sono validi per la configurazione di connessioni con coesistenza S2S/ExpressRoute.
> Per altre informazioni sull'uso di gateway in una configurazione di connessioni con coesistenza, vedere [Configurare connessioni coesistenti](../articles/expressroute/expressroute-howto-coexist-classic.md#gw)

## <a name="add-a-gateway"></a>Aggiungere un gateway

Usare il comando seguente per creare un gateway. Sostituire i valori presenti con i valori desiderati.

```powershell
New-AzureVNetGateway -VNetName "MyAzureVNET" -GatewayType DynamicRouting -GatewaySKU  Standard
```

## <a name="verify-the-gateway-was-created"></a>Verificare che il gateway sia stato creato

Utilizzare il comando seguente per verificare che il gateway sia stato creato. Questo comando recupera l'ID del gateway, necessario per le altre operazioni.

```powershell
Get-AzureVNetGateway
```

## <a name="resize-a-gateway"></a>Ridimensionare un gateway

Esistono diversi [SKU del gateway](../articles/expressroute/expressroute-about-virtual-network-gateways.md). È possibile usare il comando seguente per modificare la SKU del gateway in qualsiasi momento.

> [!IMPORTANT]
> Questo comando non funziona per il gateway UltraPerformance. Per modificare il gateway in un gateway UltraPerformance, innanzitutto rimuovere il gateway ExpressRoute esistente, quindi creare un nuovo gateway UltraPerformance. Per effettuare il downgrade del gateway da un gateway UltraPerformance, innanzitutto rimuovere il gateway UltraPerformance, quindi creare un nuovo gateway. 
>
>

```powershell
Resize-AzureVNetGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance
```

## <a name="remove-a-gateway"></a>Rimuovere un gateway

Usare il comando seguente per rimuovere un gateway

```powershell
Remove-AzureVnetGateway -GatewayId <Gateway ID>
```
