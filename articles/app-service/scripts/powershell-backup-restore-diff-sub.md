---
title: Esempio di script Azure PowerShell - Ripristinare un backup di un'app in un'altra sottoscrizione | Microsoft Docs
description: Esempio di script Azure PowerShell - Ripristinare un'app Web da un backup in un'altra sottoscrizione
services: app-service\web
documentationcenter: ''
author: msangapu
manager: jpconnoc
editor: ''
tags: azure-service-management
ms.assetid: a2a27d94-d378-4c17-a6a9-ae1e69dc4a72
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 11/21/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 9e27585d44214a2826ca2c4ac705eb6bb803344a
ms.sourcegitcommit: 7cd706612a2712e4dd11e8ca8d172e81d561e1db
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2018
ms.locfileid: "53586642"
---
# <a name="restore-a-web-app-from-a-backup-in-another-subscription-using-powershell"></a>Ripristinare un'app Web da un backup in un'altra sottoscrizione usando PowerShell

Questo script di esempio recupera un backup completato precedentemente da un'app Web esistente e lo ripristina in un'app Web in un'altra sottoscrizione. 

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/overview) e quindi eseguire `Connect-AzureRmAccount` per creare una connessione con Azure. 

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/backup-restore-diff-sub/backup-restore-diff-sub.ps1?highlight=1-6 "Restore a web app from a backup in another subscription")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Quando l'app Web non è più necessaria, usare il comando seguente per rimuovere il gruppo di risorse, l'app Web e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroupName -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount) | Aggiunge un account autenticato da usare per le richieste con cmdlet di Azure Resource Manager.  |
| [Get-AzureRmWebAppBackupList](/powershell/module/azurerm.websites/get-azurermwebappbackuplist) | Ottiene un elenco di copie di backup per un'app Web. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Crea un'App Web |
| [Restore-AzureRmWebAppBackup](/powershell/module/azurerm.websites/restore-azurermwebappbackup) | Ripristina un'app Web da un backup precedentemente completato. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../samples-powershell.md) (Esempi di Azure PowerShell).
