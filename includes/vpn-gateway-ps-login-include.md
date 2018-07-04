---
title: File di inclusione
description: File di inclusione
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: d4d370e6b76fcfc502366642842bfeb923a13991
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2018
ms.locfileid: "36313677"
---
Prima di iniziare questa configurazione, è necessario accedere all'account Azure. Il cmdlet richiede le credenziali di accesso per l'account di Azure. Dopo l'accesso vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../articles/powershell-azure-resource-manager.md).

Per accedere, aprire la console di PowerShell con privilegi elevati e connettersi al proprio account. Per eseguire la connessione, usare gli esempi che seguono:

```powershell
Connect-AzureRmAccount
```

Se si hanno più sottoscrizioni di Azure, selezionare le sottoscrizioni per l'account.

```powershell
Get-AzureRmSubscription
```

Specificare la sottoscrizione da usare.

```powershell
Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
