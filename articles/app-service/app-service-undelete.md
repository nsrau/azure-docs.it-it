---
title: Ripristinare le app del servizio App eliminate-servizio app Azure
description: Informazioni su come ripristinare un'app del servizio App eliminata usando PowerShell.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: 7b3a21f3cfee806dc94353e0bc6c11e88641ea34
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827530"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Ripristinare un'app del servizio App eliminata con PowerShell

Se l'app è stata eliminata accidentalmente nel servizio app Azure, è possibile ripristinarla usando i comandi del [modulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="list-deleted-apps"></a>Elencare le app eliminate

Per ottenere la raccolta di App eliminate, è possibile `Get-AzDeletedWebApp`usare.

Per informazioni dettagliate su un'app eliminata specifica, è possibile usare:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app>
```

Le informazioni dettagliate includono:

- **DeletedSiteId**: Identificatore univoco per l'app, usato per gli scenari in cui sono state eliminate più app con lo stesso nome
- **SubscriptionId**: Sottoscrizione che contiene la risorsa eliminata
- **Località**: Percorso dell'app originale
- **ResourceGroupName**: Nome del gruppo di risorse originale
- **Nome**: Nome dell'app originale.
- **Slot**: nome dello slot.
- **Ora eliminazione**: Quando è stata eliminata l'app  

## <a name="restore-deleted-app"></a>Ripristinare l'app eliminata

Una volta identificata l'app che si vuole ripristinare, è possibile ripristinarla usando `Restore-AzDeletedWebApp`.

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```

Gli input per il comando sono:

- **Gruppo di risorse**: Gruppo di risorse di destinazione in cui verrà ripristinata l'app
- **Nome**: Il nome dell'app deve essere globalmente univoco.
- **TargetAppServicePlanName**: Piano di servizio app collegato all'app

Per impostazione `Restore-AzDeletedWebApp` predefinita, viene ripristinata anche la configurazione dell'app e un contenuto. Se si vuole ripristinare solo il contenuto, usare il `-RestoreContentOnly` flag con questo cmdlet.

Qui è possibile trovare il riferimento completo a cmdlet: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
