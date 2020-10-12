---
title: includere file
description: includere file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/10/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 50ce8530aca40eed07741f35be1a57bbd7cc1868
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "77133615"
---
Aprire la console di PowerShell con privilegi elevati.

Se si usa Azure PowerShell in locale, connettersi al proprio account Azure. Il cmdlet *Connect-AzAccount* richiede le credenziali. Dopo l'autenticazione vengono scaricate le impostazioni dell'account, in modo che siano disponibili per Azure PowerShell. Se invece si usa Azure Cloud Shell, non è necessario eseguire *Connect-AzAccount*. Azure Cloud Shell si connette automaticamente all'account Azure.

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