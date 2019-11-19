---
title: Ripristinare le app del servizio App eliminate-servizio app Azure
description: Informazioni su come ripristinare un'app del servizio App eliminata usando PowerShell.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.service: app-service
ms.openlocfilehash: 6a3a62053a488f95e22cae13ef9d0714a7b5dd05
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74173732"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Ripristinare un'app del servizio App eliminata con PowerShell

Se l'app è stata eliminata accidentalmente nel servizio app Azure, è possibile ripristinarla usando i comandi del [modulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

## <a name="re-register-app-service-resource-provider"></a>Registrare di nuovo il provider di risorse del servizio app
Alcuni clienti potrebbero riscontrare un problema a causa del quale il recupero dell'elenco di App eliminate ha esito negativo. Per risolvere il problema, eseguire il comando seguente:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Elencare le app eliminate

Per ottenere la raccolta di App eliminate, è possibile usare `Get-AzDeletedWebApp`.

Per informazioni dettagliate su un'app eliminata specifica, è possibile usare:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
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
