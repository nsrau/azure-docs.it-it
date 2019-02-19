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
ms.openlocfilehash: 845d47af75495a158e56312126c8e246b43f789f
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/12/2019
ms.locfileid: "56112769"
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
| [Add-AzAccount](/powershell/module/az.profile/add-azaccount) | Aggiunge un account autenticato da usare per le richieste con cmdlet di Azure Resource Manager.  |
| [Get-AzWebAppBackupList](/powershell/module/az.websites/get-azwebappbackuplist) | Ottiene un elenco di copie di backup per un'app Web. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crea un'App Web |
| [Restore-AzWebAppBackup](/powershell/module/az.websites/restore-azwebappbackup) | Ripristina un'app Web da un backup precedentemente completato. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../samples-powershell.md) (Esempi di Azure PowerShell).
