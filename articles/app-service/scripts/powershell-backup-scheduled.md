---
title: 'PowerShell: Creare un backup pianificato'
description: Informazioni su come usare Azure PowerShell per automatizzare la distribuzione e la gestione di Servizio app. Questo esempio illustra come creare un backup pianificato per un'app.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b20627dbeae722e31b042cf58ba4029ac8225555
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685783"
---
# <a name="create-a-scheduled-backup-for-a-web-app-using-powershell"></a>Creare un backup programmato per un'app Web con PowerShell

Questo script di esempio crea un'app Web nel servizio app con le relative risorse e quindi crea un backup pianificato per tale app. 

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/overview) e quindi eseguire `Connect-AzAccount` per creare una connessione con Azure. 

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-scheduled/backup-scheduled.ps1?highlight=1-4 "Create a scheduled backup for a web app")]

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
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Crea un account di archiviazione. |
| [New-AzStorageContainer](/powershell/module/az.storage/new-AzStoragecontainer) | Crea un contenitore di Archiviazione di Azure. |
| [New-AzStorageContainerSASToken](/powershell/module/az.storage/new-AzStoragecontainersastoken) | Genera un token di firma di accesso condiviso per un contenitore di archiviazione di Azure. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Consente di creare un piano di servizio app. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crea un'App Web. |
| [Edit-AzWebAppBackupConfiguration](/powershell/module/az.websites/edit-azwebappbackupconfiguration) | Modifica la configurazione del backup per l'app Web. |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Ottiene un elenco di copie di backup per un'app Web. |
| [Get-AzWebAppBackupConfiguration](/powershell/module/az.websites/get-azwebappbackupconfiguration) | Ottiene la configurazione del backup per l'app Web. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../samples-powershell.md) (Esempi di Azure PowerShell).
