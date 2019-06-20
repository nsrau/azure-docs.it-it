---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/17/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 49f48c2d0bf865cf8c8fde831e7a597f8701d213
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179975"
---
Per verificare l'esito positivo della connessione è possibile usare il cmdlet 'Get-AzVirtualNetworkGatewayConnection' con o senza '-Debug'. 

1. Per configurare i valori in modo che corrispondano ai propri, usare l'esempio di cmdlet seguente. Quando richiesto, selezionare "A" per eseguire "Tutti". Il valore " -Name" nell'esempio fa riferimento al nome della connessione che si vuole testare.

   ```azurepowershell-interactive
   Get-AzVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. Al termine dell'esecuzione del cmdlet, visualizzare i valori. Nell'esempio seguente lo stato di connessione risulta "Connesso" ed è possibile visualizzare i byte in ingresso e in uscita.
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```