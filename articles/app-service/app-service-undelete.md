---
title: Ripristinare le app eliminate
description: Informazioni su come ripristinare un'app eliminata nel servizio app di Azure.Learn how to restore a deleted app in Azure App Service. Evita il mal di testa di un'app eliminata accidentalmente.
author: btardif
ms.author: byvinyal
ms.date: 9/23/2019
ms.topic: article
ms.openlocfilehash: 296c8e2dfe99e3b0aea66f364ac6f6d9b2f60a1a
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272492"
---
# <a name="restore-deleted-app-service-app-using-powershell"></a>Ripristinare un'app del servizio App eliminata con PowerShell

Se l'app verrà eliminata accidentalmente nel servizio app di Azure, è possibile ripristinarla usando i comandi del [modulo Az PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-2.6.0&viewFallbackFrom=azps-2.2.0).

> [!NOTE]
> Le app eliminate vengono eliminate dal sistema 30 giorni dopo l'eliminazione iniziale. Una volta che un'app è stata eliminata, non può essere recuperata.
>

## <a name="re-register-app-service-resource-provider"></a>Registrare nuovamente il provider di risorse del servizio appRe-register App Service resource provider
Alcuni clienti potrebbero riscontrare un problema a cui il recupero dell'elenco delle app eliminate non riesce. Per risolvere il problema, eseguire il comando seguente:

```powershell
 Register-AzResourceProvider -ProviderNamespace "Microsoft.Web"
```

## <a name="list-deleted-apps"></a>Elencare le app eliminate

Per ottenere la raccolta di app `Get-AzDeletedWebApp`eliminate, è possibile utilizzare .

Per informazioni dettagliate su un'app eliminata specifica che puoi usare:

```powershell
Get-AzDeletedWebApp -Name <your_deleted_app> -Location <your_deleted_app_location> 
```

Le informazioni dettagliate includono:

- **DeletedSiteId**: Identificatore univoco per l'app, usato per scenari in cui sono state eliminate più app con lo stesso nome
- **SubscriptionID**: Sottoscrizione contenente la risorsa eliminataSubscriptionID : Subscription containing the deleted resource
- **Posizione**: Posizione dell'app originale
- **ResourceGroupName:** nome del gruppo di risorse originaleResourceGroupName: nome del gruppo di risorse originaleResourceGroupName: nome del gruppo di risorse originaleResourceGroup
- **Nome**: Nome dell'app originale.
- **Slot**: il nome dello slot.
- **Tempo di eliminazione**: Quando è stata eliminata l'app  

## <a name="restore-deleted-app"></a>Ripristinare l'app eliminata

Una volta identificata l'app che si desidera `Restore-AzDeletedWebApp`ripristinare, è possibile ripristinarla utilizzando .

```powershell
Restore-AzDeletedWebApp -ResourceGroupName <my_rg> -Name <my_app> -TargetAppServicePlanName <my_asp>
```
> [!NOTE]
> Gli slot di distribuzione non vengono ripristinati come parte dell'app. Se è necessario ripristinare uno `-Slot <slot-name>` slot di gestione temporanea, utilizzare il flag.
>

Gli input per il comando sono:

- **Gruppo di risorse:** gruppo di risorse di destinazione in cui verrà ripristinata l'app
- **Nome**: Nome per l'app, deve essere univoco a livello globale.
- **TargetAppServicePlanName:** piano di servizio app collegato all'app

Per `Restore-AzDeletedWebApp` impostazione predefinita, verranno ripristinate sia la configurazione dell'app che un contenuto. Se si desidera ripristinare solo il `-RestoreContentOnly` contenuto, utilizzare il flag con questo commandlet.

> [!NOTE]
> Se l'app è stata ospitata e quindi eliminata da un ambiente del servizio app, è possibile ripristinarla solo se esiste ancora l'ambiente del servizio app corrispondente.
>

È possibile trovare il riferimento al commandlet completo qui: [Restore-AzDeletedWebApp](https://docs.microsoft.com/powershell/module/az.websites/restore-azdeletedwebapp).
