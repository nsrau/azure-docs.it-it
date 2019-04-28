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
ms.openlocfilehash: 853bd32cf3ea97571929d54fb7d3ba04bde16b81
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405845"
---
Prima di iniziare questa configurazione, è necessario accedere all'account Azure. Il cmdlet richiede le credenziali di accesso per l'account di Azure. Dopo l'accesso vengono scaricate le impostazioni dell'account in modo che siano disponibili per Azure PowerShell. Per altre informazioni, vedere [Uso di Windows PowerShell con Gestione risorse](../articles/powershell-azure-resource-manager.md).

Per accedere, aprire la console di PowerShell con privilegi elevati e connettersi al proprio account. Per eseguire la connessione, usare gli esempi che seguono:

```powershell
Connect-AzAccount
```

Se si hanno più sottoscrizioni di Azure, selezionare le sottoscrizioni per l'account.

```powershell
Get-AzSubscription
```

Specificare la sottoscrizione da usare.

```powershell
Select-AzSubscription -SubscriptionName "Replace_with_your_subscription_name"
 ```
