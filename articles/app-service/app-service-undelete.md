---
title: Ripristinare le app del servizio App eliminate-servizio app Azure
description: Informazioni su come ripristinare un'app del servizio App eliminata usando PowerShell.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: 7dc3934f486b205febd5be3c0b484dfd2c97bb8f
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755537"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Ripristinare un'app del servizio App eliminata con PowerShell

Se l'app è stata eliminata accidentalmente nel servizio app Azure, è possibile ripristinarla usando i comandi del [modulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="list-deleted-apps"></a>Elencare le app eliminate

Per ottenere la raccolta di App eliminate, è possibile usare `Get-AzDeletedWebApp`.

Per informazioni dettagliate su un'app eliminata specifica, è possibile usare:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

Le informazioni dettagliate includono:

- **DeletedSiteId**: identificatore univoco per l'app, usato per gli scenari in cui sono state eliminate più app con lo stesso nome
- **SubscriptionId**: sottoscrizione che contiene la risorsa eliminata
- **Location**: percorso dell'app originale
- **ResourceGroupName**: nome del gruppo di risorse originale
- **Nome**: nome dell'app originale.
- **Slot**: nome dello slot.
- **Ora eliminazione**: quando è stata eliminata l'app  

## <a name="restore-deleted-app"></a>Ripristinare l'app eliminata

Una volta identificata l'app che si vuole ripristinare, è possibile ripristinarla usando `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Gli input per il comando sono:

- **Gruppo di risorse**: gruppo di risorse di destinazione in cui verrà ripristinata l'app
- **Nome**: il nome dell'app deve essere univoco a livello globale.
- **TargetAppServicePlanName**: piano di servizio app collegato all'app

Per impostazione predefinita `Restore-AzDeletedWebApp` Ripristina sia la configurazione dell'app che un contenuto. Se si vuole ripristinare solo il contenuto, usare il flag `-RestoreContentOnly` con questo cmdlet.

> [!NOTE]
> Se l'app è stata ospitata in e quindi eliminata da un ambiente del servizio app, può essere ripristinata solo se la ambiente del servizio app corrispondente esiste ancora.
>

Qui è possibile trovare il riferimento completo a cmdlet: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
