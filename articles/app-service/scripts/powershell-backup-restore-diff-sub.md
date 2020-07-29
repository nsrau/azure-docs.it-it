---
title: "PowerShell: Ripristinare il backup in un'altra sottoscrizione"
description: Informazioni su come usare Azure PowerShell per automatizzare la distribuzione e la gestione di Servizio app. Questo esempio illustra come ripristinare un backup in un'altra sottoscrizione.
author: msangapu-msft
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: mvc, seodec18
ms.openlocfilehash: c9dda1f3e011006d1b5072c5b71accb28963aedb
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004869"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Ripristinare un'app Web da un backup in un'altra sottoscrizione usando PowerShell

Questo script di esempio recupera un backup completato precedentemente da un'app Web esistente e lo ripristina in un'app Web in un'altra sottoscrizione. 

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/) e quindi eseguire `Connect-AzAccount` per creare una connessione con Azure. 

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

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../samples-powershell.md) (Esempi di Azure PowerShell).
