---
title: Esempio di script di Azure PowerShell - Connettere un'app a un account di archiviazione | Microsoft Docs
description: Esempio di script di Azure PowerShell - Connettere un'app del servizio app a un account di archiviazione
services: app-service\web
documentationcenter: ''
author: syntaxc4
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: e4831bdc-2068-4883-9474-0b34c2e3e255
ms.service: app-service
ms.devlang: multiple
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/20/2017
ms.author: cfowler
ms.custom: mvc
ms.openlocfilehash: 851d8e0c8d7e7a746af2f364ab986f8e5f679a84
ms.sourcegitcommit: e68df5b9c04b11c8f24d616f4e687fe4e773253c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/20/2018
ms.locfileid: "53650525"
---
# <a name="connect-an-app-service-app-to-a-storage-account"></a>Connettere un'app del servizio app a un account di archiviazione

Questo scenario illustra come creare un account di archiviazione di Azure e un'app del servizio app. L'account di archiviazione viene quindi collegato all'app usando le impostazioni dell'app.

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/overview) e quindi eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.

## <a name="sample-script"></a>Script di esempio

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/connect-to-storage/connect-to-storage.ps1 "Connect an app to a storage account")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dello script di esempio, eseguire questo comando per rimuovere il gruppo di risorse, l'app del servizio app e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Consente di creare un piano di servizio app. |
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Consente di creare un'app del servizio app. |
| [New-AzureRMStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) | Crea un account di archiviazione. |
| [Get-AzureRMStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | Ottiene le chiavi di accesso per l'account di Archiviazione di Azure. |
| [Set-AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | Modifica la configurazione di un'app del servizio app. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per il Servizio app di Azure sono disponibili in [Esempi di Azure PowerShell](../samples-powershell.md).
