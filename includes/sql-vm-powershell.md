---
author: MikeRayMSFT
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 11/25/2018
ms.author: mikeray
ms.openlocfilehash: 27da28073b01c6bc43868172d6c989d9518d8f53
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2020
ms.locfileid: "95559925"
---
## <a name="start-your-powershell-session"></a>Avviare la sessione di PowerShell
 

Eseguire il cmdlet [**Connect-Az Account**](/powershell/module/Az.Accounts/Connect-AzAccount) per visualizzare una schermata di accesso in cui immettere le credenziali. Usare le stesse credenziali usate per l'accesso al portale di Azure.

```powershell
Connect-AzAccount
```

Se si hanno più sottoscrizioni, usare il cmdlet [**Set-AzContext**](/powershell/module/az.accounts/set-azcontext) per selezionare la sottoscrizione da usare nella sessione di PowerShell. Per visualizzare la sottoscrizione in uso nella sessione corrente di PowerShell, eseguire [**Get-AzContext**](/powershell/module/az.accounts/get-azcontext). Per visualizzare tutte le sottoscrizioni, eseguire [**Get-AzSubscription**](/powershell/module/az.accounts/get-azsubscription).

```powershell
Set-AzContext -SubscriptionId '4cac86b0-1e56-bbbb-aaaa-000000000000'
```