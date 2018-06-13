---
title: 'Reimpostare un circuito Azure ExpressRoute con un errore: PowerShell | Microsoft Docs'
description: Questo articolo illustra come reimpostare un circuito ExpressRoute in stato di errore.
documentationcenter: na
services: expressroute
author: anzaman
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/28/2017
ms.author: anzaman;cherylmc
ms.openlocfilehash: 423bc1d6409e5b7fe02339a05d0775f4ff42de49
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/19/2018
ms.locfileid: "31590684"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Reimpostare un circuito ExpressRoute con un errore

Quando un'operazione su un circuito ExpressRoute non viene completata correttamente, il circuito può entrare in uno stato di errore. Questo articolo illustra come reimpostare un circuito ExpressRoute con un errore.

## <a name="reset-a-circuit"></a>Reimpostare un circuito

1. Installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

2. Aprire la console di PowerShell con privilegi elevati e connettersi al proprio account. Per eseguire la connessione, usare gli esempi che seguono:

  ```powershell
  Connect-AzureRmAccount
  ```
3. Se si hanno più sottoscrizioni di Azure, selezionare le sottoscrizioni per l'account.

  ```powershell
  Get-AzureRmSubscription
  ```
4. Specificare la sottoscrizione da usare.

  ```powershell
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Eseguire i comandi seguenti per reimpostare un circuito in stato di errore:

  ```powershell
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

Il circuito dovrebbe ora essere integro. Se lo stato di errore persiste, aprire un ticket di supporto per il [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Passaggi successivi

Aprire un ticket di assistenza al [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se continuano a verificarsi problemi.
