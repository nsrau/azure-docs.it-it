---
title: "PowerShell: Eliminare un backup dell'app"
description: Informazioni su come usare Azure PowerShell per automatizzare la distribuzione e la gestione di Servizio app. Questo esempio illustra come eliminare il backup di un'app.
author: msangapu-msft
tags: azure-service-management
ms.assetid: ebcadb49-755d-4202-a5eb-f211827a9168
ms.topic: sample
ms.date: 10/30/2017
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: b8d3175b4266e132d94f6f2dde163cb60fb11396
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685807"
---
# <a name="delete-a-backup-for-a-web-using-azure-powershell"></a>Eliminare un backup per un'app Web usando Azure PowerShell

Questo script di esempio crea un'app Web nel servizio app con le relative risorse correlate e quindi crea un backup singolo di tale app. 

Per eseguire questo script, è necessario un backup esistente di un'app Web. Per crearne uno, vedere [Eseguire il backup di un'app Web](powershell-backup-onetime.md) o [Creare un backup pianificato per un'app Web](powershell-backup-scheduled.md).

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-delete/backup-delete.ps1?highlight=1-2,11 "Delete a backup for a web app")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo aver eseguito lo script di esempio, usare il comando seguente per rimuovere il gruppo di risorse, l'App Web e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Ottiene un elenco di copie di backup per un'app Web. |
| [Remove-AzWebAppBackup](/powershell/module/az.websites/remove-azwebappbackup) | Rimuove il backup specificato di un'app Web. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../samples-powershell.md) (Esempi di Azure PowerShell).
