---
title: 'PowerShell: Assegnare un dominio personalizzato'
description: Informazioni su come usare Azure PowerShell per automatizzare la distribuzione e la gestione di Servizio app. Questo esempio illustra come assegnare un dominio personalizzato a un'app.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 356f5af9-f62e-411c-8b24-deba05214103
ms.topic: sample
ms.date: 03/20/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: f7de667292ed89caa2895f6db3f7d8d6abf15f85
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685594"
---
# <a name="assign-a-custom-domain-to-a-web-app-using-powershell"></a>Assegnare un dominio personalizzato a un'app Web tramite PowerShell

Questo script di esempio crea un'App Web nel servizio App con le relative risorse correlate e quindi esegue il mapping di `www.<yourdomain>` a essa. 

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/overview) e quindi eseguire `Connect-AzAccount` per creare una connessione con Azure. Occorre inoltre disporre dell'accesso alla pagina di configurazione DNS del registrar di dominio.

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/map-custom-domain/map-custom-domain.ps1?highlight=1 "Assign a custom domain to a web app")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo aver eseguito lo script di esempio, usare il comando seguente per rimuovere il gruppo di risorse, l'App Web e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Consente di creare un piano di servizio app. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crea un'App Web. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Modifica un piano di servizio app per cambiarne il piano tariffario. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Modifica la configurazione di un'app Web. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../samples-powershell.md) (Esempi di Azure PowerShell).
