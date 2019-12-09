---
title: "PowerShell: Ripristinare il backup in un'altra sottoscrizione"
description: Informazioni su come usare Azure PowerShell per automatizzare la distribuzione e la gestione di Servizio app. Questo esempio illustra come ripristinare un backup in un'altra sottoscrizione.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 8168fbedd1a639dce0ab8bd076e14ab24e2f6d1e
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685811"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Ripristinare un'app Web da un backup in un'altra sottoscrizione usando PowerShell

Questo script di esempio recupera un backup completato precedentemente da un'app Web esistente e lo ripristina in un'app Web in un'altra sottoscrizione. 

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/overview) e quindi eseguire `Connect-AzAccount` per creare una connessione con Azure. 

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Quando l'app Web non è più necessaria, usare il comando seguente per rimuovere il gruppo di risorse, l'app Web e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Add-AzAccount](/powershell/module/az.accounts/connect-azaccount) | Aggiunge un account autenticato da usare per le richieste con cmdlet di Azure Resource Manager.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Ottiene un elenco di copie di backup per un'app Web. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crea un'App Web |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Ripristina un'app Web da un backup precedentemente completato. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../samples-powershell.md) (Esempi di Azure PowerShell).
