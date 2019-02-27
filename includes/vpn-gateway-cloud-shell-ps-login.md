---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: deabef0c2c3540e515fe72a161710c95a20fa86f
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418066"
---
Aprire la console di PowerShell con privilegi elevati.



Se si usa Azure PowerShell in locale, connettersi al proprio account Azure. Il cmdlet *Connect-AzAccount* richiede le credenziali. Dopo l'autenticazione vengono scaricate le impostazioni dell'account, in modo che siano disponibili per Azure PowerShell. Se non si esegue PowerShell in locale e si usa l'opzione Prova di Azure Cloud Shell nel browser, ignorare il primo passaggio. La connessione all'account Azure avviene automaticamente.

```azurepowershell
Connect-AzAccount
```

Se si dispone di più sottoscrizioni, ottenere un elenco delle sottoscrizioni di Azure.

```azurepowershell-interactive
Get-AzSubscription
```

Specificare la sottoscrizione da usare.

```azurepowershell-interactive
Select-AzSubscription -SubscriptionName "Name of subscription"
```