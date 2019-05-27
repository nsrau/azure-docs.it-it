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
ms.openlocfilehash: 1aca39a7ff162aa3c42fdb3ca5999c71091ec02e
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66119405"
---
 Se si usa Azure Cloud Shell, accedi al tuo account Azure automaticamente dopo aver fatto clic "Prova". Per accedere in locale, aprire la console di PowerShell con privilegi elevati ed eseguire il cmdlet per la connessione.

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