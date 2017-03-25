---
title: Esempio di script di Azure PowerShell - Associare un certificato SSL personalizzato a un&quot;app Web | Microsoft Docs
description: Esempio di script di Azure PowerShell - Associare un certificato SSL personalizzato a un&quot;app Web
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 9b8d26edbdd983eed521e4e1737077bd639532d9
ms.lasthandoff: 03/10/2017

---

# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Associare un certificato SSL personalizzato a un'app Web

Questo script di esempio crea un'app Web nel servizio app con le relative risorse correlate, quindi associa ad essa il certificato SSL di un nome di dominio personalizzato. 

Prima di eseguire questo script verificare che:

- È stata creata una connessione con Azure usando il comando `az login`.
- Si disponga dell'accesso alla pagina di configurazione DNS del registrar.
- Si disponga di un file con estensione PFX valido e della relativa password per il certificato SSL da caricare e per cui si desidera eseguire l'associazione.

## <a name="sample-script"></a>Script di esempio

[!code-powershell[principale](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Associare un certificato SSL personalizzato a un'app Web")]

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
| [Set-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermappserviceplan) | Modifica un piano di servizio app per cambiarne il piano tariffario. |
| [Set-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/set-azurermwebapp) | Modifica la configurazione di un'app Web. |
| [New-AzureRmWebAppSSLBinding](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebappsslbinding) | Crea un'associazione al certificato SSL per un'app Web. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../app-service-powershell-samples.md) (Esempi di Azure PowerShell).

