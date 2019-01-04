---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 11/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fee97b29f24d8bb4f50a2929c3ceb33af85a5e21
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52852364"
---
Aprire la console di PowerShell con privilegi elevati.



Se si usa Azure PowerShell in locale, connettersi al proprio account Azure. Il cmdlet *Connect-AzureRmAccount* richiede le credenziali. Dopo l'autenticazione vengono scaricate le impostazioni dell'account, in modo che siano disponibili per Azure PowerShell. Se non si esegue PowerShell in locale e si usa l'opzione Prova di Azure Cloud Shell nel browser, ignorare il primo passaggio. La connessione all'account Azure avviene automaticamente.

```azurepowershell
Connect-AzureRmAccount
```

Se si dispone di più sottoscrizioni, ottenere un elenco delle sottoscrizioni di Azure.

```azurepowershell-interactive
Get-AzureRmSubscription
```

Specificare la sottoscrizione da usare.

```azurepowershell-interactive
Select-AzureRmSubscription -SubscriptionName "Name of subscription"
```