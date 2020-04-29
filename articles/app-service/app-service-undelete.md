---
title: Ripristinare le app eliminate
description: Informazioni su come ripristinare un'app eliminata nel servizio app Azure. Evitare la cefalea di un'app eliminata accidentalmente.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 296c8e2dfe99e3b0aea66f364ac6f6d9b2f60a1a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272492"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Ripristinare un'app del servizio App eliminata con PowerShell

Se l'app è stata eliminata accidentalmente nel servizio app Azure, è possibile ripristinarla usando i comandi del [modulo AZ PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> Le app eliminate vengono eliminate dal sistema 30 giorni dopo l'eliminazione iniziale. Una volta che un'app è stata eliminata, non è possibile recuperarla.
>

## <a name="re-register-app-service-resource-provider"></a>Registrare di nuovo il provider di risorse del servizio app
Alcuni clienti potrebbero riscontrare un problema a causa del quale il recupero dell'elenco di App eliminate ha esito negativo. Per risolvere il problema, eseguire il comando seguente:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Elencare le app eliminate

Per ottenere la raccolta di App eliminate, è possibile `Get-AzDeletedWebApp`usare.

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
> [!NOTE]
> Gli slot di distribuzione non vengono ripristinati come parte dell'app. Se è necessario ripristinare uno slot di staging, usare `-Slot <slot-name>` il flag.
>

Gli input per il comando sono:

- **Gruppo di risorse**: gruppo di risorse di destinazione in cui verrà ripristinata l'app
- **Nome**: il nome dell'app deve essere univoco a livello globale.
- **TargetAppServicePlanName**: piano di servizio app collegato all'app

Per impostazione `Restore-AzDeletedWebApp` predefinita, viene ripristinata anche la configurazione dell'app e un contenuto. Se si vuole ripristinare solo il contenuto, usare il `-RestoreContentOnly` flag con questo cmdlet.

> [!NOTE]
> Se l'app è stata ospitata in e quindi eliminata da un ambiente del servizio app, può essere ripristinata solo se la ambiente del servizio app corrispondente esiste ancora.
>

Qui è possibile trovare il riferimento completo a cmdlet: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
