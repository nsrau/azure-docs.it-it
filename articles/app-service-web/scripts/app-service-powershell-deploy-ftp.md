---
title: Esempio di script di Azure PowerShell - Caricare i file in un&quot;app Web usando FTP | Microsoft Docs
description: Esempio di script di Azure PowerShell - Caricare i file in un&quot;app Web usando FTP
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: b7d46d6f-44fd-454c-8008-87dab6eefbc1
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: f4729e9daf788220358e7cca2508c820a1f2006a
ms.lasthandoff: 03/21/2017

---

# <a name="upload-files-to-a-web-app-using-ftp"></a>Caricare i file in un'app Web tramite FTP

Questo script di esempio crea un'App Web nel servizio app con le risorse correlate e quindi distribuisce il codice dell'App Web tramite FTP (con [WebClient.UploadFile()](https://msdn.microsoft.com/library/ms144229.aspx)).

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e quindi eseguire `Login-AzureRmAccount` per creare una connessione con Azure.

## <a name="sample-script"></a>Script di esempio

[!code-powershell[principale](../../../powershell_scripts/app-service/deploy-ftp/deploy-ftp.ps1?highlight=1 "Caricare i file in un'app Web tramite FTP")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo aver eseguito lo script di esempio, usare il comando seguente per rimuovere il gruppo di risorse, l'App Web e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | Consente di creare un piano di servizio app. |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Crea un'App Web. |
| [Get-AzureRmWebAppPublishingProfile](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/get-azurermwebapppublishingprofile) | Ottenere un profilo di pubblicazione delle app Web. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../app-service-powershell-samples.md) (Esempi di Azure PowerShell).

