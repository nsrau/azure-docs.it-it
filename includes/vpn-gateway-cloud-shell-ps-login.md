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
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/10/2019
ms.locfileid: "54201483"
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