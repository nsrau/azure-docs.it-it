---
title: 'Reimpostare un circuito ExpressRoute non riuscito: PowerShell: Azure | Microsoft Docs'
description: Questo articolo illustra come reimpostare un circuito ExpressRoute in stato di errore.
services: expressroute
author: anzaman
ms.service: expressroute
ms.topic: article
ms.date: 11/28/2018
ms.author: anzaman
ms.custom: seodec18
ms.openlocfilehash: deeb1c65cae7e3a5b42230dbda1ad8efa717ba0b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748098"
---
# <a name="reset-a-failed-expressroute-circuit"></a>Reimpostare un circuito ExpressRoute con un errore

Quando un'operazione su un circuito ExpressRoute non viene completata correttamente, il circuito può entrare in uno stato di errore. Questo articolo illustra come reimpostare un circuito ExpressRoute con un errore.

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

## <a name="reset-a-circuit"></a>Reimpostare un circuito

1. Installare la versione più recente dei cmdlet di PowerShell per Azure Resource Manager. Per altre informazioni, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).

2. Aprire la console di PowerShell con privilegi elevati e connettersi al proprio account. Per eseguire la connessione, usare gli esempi che seguono:

   ```azurepowershell-interactive
   Connect-AzAccount
   ```
3. Se si hanno più sottoscrizioni di Azure, selezionare le sottoscrizioni per l'account.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```
4. Specificare la sottoscrizione da usare.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
   ```
5. Eseguire i comandi seguenti per reimpostare un circuito in stato di errore:

   ```azurepowershell-interactive
   $ckt = Get-AzExpressRouteCircuit -Name "ExpressRouteARMCircuit" -ResourceGroupName "ExpressRouteResourceGroup"

   Set-AzExpressRouteCircuit -ExpressRouteCircuit $ckt
   ```

Il circuito dovrebbe ora essere integro. Se lo stato di errore persiste, aprire un ticket di supporto per il [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Passaggi successivi

Aprire un ticket di assistenza al [supporto tecnico Microsoft](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) se continuano a verificarsi problemi.
