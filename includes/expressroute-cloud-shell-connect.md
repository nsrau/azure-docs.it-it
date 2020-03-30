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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "67179880"
---
 Se si usa Azure Cloud Shell, accedere automaticamente all'account Azure dopo aver fatto clic su "Prova". Per accedere localmente, aprire la console di PowerShell con privilegi elevati ed eseguire il cmdlet per connettersi.

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