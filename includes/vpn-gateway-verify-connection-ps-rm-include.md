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
ms.openlocfilehash: 267234cb9ecea1dc097f13739bf98ee11206ad06
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2018
ms.locfileid: "53444181"
---
Per verificare l'esito positivo della connessione è possibile usare il cmdlet 'Get-AzureRmVirtualNetworkGatewayConnection' con o senza '-Debug'. 

1. Per configurare i valori in modo che corrispondano ai propri, usare l'esempio di cmdlet seguente. Quando richiesto, selezionare "A" per eseguire "Tutti". Il valore " -Name" nell'esempio fa riferimento al nome della connessione che si vuole testare.

   ```azurepowershell-interactive
   Get-AzureRmVirtualNetworkGatewayConnection -Name VNet1toSite1 -ResourceGroupName TestRG1
   ```
2. Al termine dell'esecuzione del cmdlet, visualizzare i valori. Nell'esempio seguente lo stato di connessione risulta "Connesso" ed è possibile visualizzare i byte in ingresso e in uscita.
   
   ```
   "connectionStatus": "Connected",
   "ingressBytesTransferred": 33509044,
   "egressBytesTransferred": 4142431
   ```