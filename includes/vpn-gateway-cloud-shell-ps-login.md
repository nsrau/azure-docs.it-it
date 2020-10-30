---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 24d146da7946176c92902698d0f52ae01baf79ee
ms.sourcegitcommit: 4f4a2b16ff3a76e5d39e3fcf295bca19cff43540
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2020
ms.locfileid: "93061624"
---
Se si esegue PowerShell in locale, aprire la console di PowerShell con privilegi elevati e connettersi al proprio account Azure. Il cmdlet *Connect-AzAccount* richiede le credenziali. Dopo l'autenticazione vengono scaricate le impostazioni dell'account, in modo che siano disponibili per Azure PowerShell.

Se si usa Azure Cloud Shell anziché eseguire PowerShell localmente, si noterà che non è necessario eseguire *Connect-AzAccount* . Azure Cloud Shell si connette automaticamente all'account Azure dopo aver selezionato **prova** .

1. Se si esegue PowerShell in locale, eseguire l'accesso.

   ```azurepowershell
   Connect-AzAccount
   ```

1. Se si dispone di più sottoscrizioni, ottenere un elenco delle sottoscrizioni di Azure.

   ```azurepowershell-interactive
   Get-AzSubscription
   ```

1. Specificare la sottoscrizione da usare.

   ```azurepowershell-interactive
   Select-AzSubscription -SubscriptionName "Name of subscription"
   ```
