---
title: Esempio di script di Azure PowerShell - Connettere un&quot;app Web a un account di archiviazione | Microsoft Docs
description: Esempio di script di Azure PowerShell - Connettere un&quot;app Web a un account di archiviazione
services: app-service\web
documentationcenter: 
author: syntaxc4
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: e4831bdc-2068-4883-9474-0b34c2e3e255
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 03/08/2017
ms.author: cfowler
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 923cfd624ef0f77bd59c4b9d196172bfd69ad806
ms.lasthandoff: 03/10/2017

---

# <a name="connect-a-web-app-to-a-storage-account"></a>Connettere un'App Web a un account di archiviazione

Questo scenario illustra come creare un account di archiviazione di Azure e un'App Web di Azure. L'account di archiviazione viene quindi collegato all'App Web usando le impostazioni dell'app.

Prima di eseguire questo script, verificare che sia stata creata una connessione con Azure tramite il cmdlet `Login-AzureRmAccount`.

## <a name="sample-script"></a>Script di esempio

[!code-powershell[principale](../../../powershell_scripts/app-service/connect-to-storage/connect-to-storage.ps1 "Connettere un'app Web a un account di archiviazione")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo l'esecuzione dello script di esempio, eseguire il comando seguente per rimuovere il gruppo di risorse, l'app Web e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | Consente di creare un piano di servizio app. |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Crea un'App Web. |
| [New-AzureRMStorageAccount](https://docs.microsoft.com/powershell/resourcemanager/azurerm.storage/v2.5.0/New-AzureRmStorageAccount) | Crea un account di archiviazione. |
| [Get-AzureRMStorageAccountKey](https://docs.microsoft.com/powershell/resourcemanager/azurerm.storage/v2.5.0/get-azurermstorageaccountkey) | Ottiene le chiavi di accesso per l'account di Archiviazione di Azure. |
| [Set-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermwebapp) | Modifica la configurazione di un'app Web. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../app-service-powershell-samples.md) (Esempi di Azure PowerShell).

