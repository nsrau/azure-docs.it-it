---
title: 'Reimpostare un circuito con errore - ExpressRoute: PowerShell: Azure | Microsoft Docs'
description: Questo articolo illustra come reimpostare un circuito ExpressRoute in stato di errore.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: e94ba2f7f6981355f4736683b891fbdced001d03
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/22/2019
ms.locfileid: "54425340"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Reimpostare un circuito ExpressRoute con un errore

Quando un'operazione su un circuito ExpressRoute non viene completata correttamente, il circuito può entrare in uno stato di errore. Questo articolo illustra come reimpostare un circuito ExpressRoute con un errore.

## <a name="reset-a-circuit"></a>Reimpostare un circuito

1. Installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni, vedere [Installare e configurare Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).

2. Aprire la console di PowerShell con privilegi elevati e connettersi al proprio account. Per eseguire la connessione, usare gli esempi che seguono:

  ```azurepowershell-interactive
  Connect-AzureRmAccount
  ```
3. Se si hanno più sottoscrizioni di Azure, selezionare le sottoscrizioni per l'account.

  ```azurepowershell-interactive
  Get-AzureRmSubscription
  ```
4. Specificare la sottoscrizione da usare.

  ```azurepowershell-interactive
  Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
  ```
5. Eseguire i comandi seguenti per reimpostare un circuito in stato di errore:

  ```azurepowershell-interactive
  $ckt = Get-AzureRmExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

  Set-AzureRmExpressRouteCircuit -ExpressRouteCircuit $ckt
  ```

Il circuito dovrebbe ora essere integro. Se lo stato di errore persiste, aprire un ticket di supporto per il [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Passaggi successivi

Aprire un ticket di assistenza al [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se continuano a verificarsi problemi.
