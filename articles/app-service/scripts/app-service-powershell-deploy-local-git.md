---
title: Esempio di script di Azure PowerShell - Creare un'app Web e distribuire il codice da un repository Git locale | Microsoft Docs
description: Esempio di script di Azure PowerShell - Creare un'app Web e distribuire il codice da un repository Git locale
services: app-service\web
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 6846d9d25fb4b6b884e39676f8dbaa6c2899436b
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/16/2018
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Creare un'App Web e distribuire il codice da un archivio Git locale

Questo script di esempio crea un'app Web nel servizio app con le relative risorse correlate e quindi distribuisce il codice dell'app Web da un repository Git locale.

Se necessario, effettuare l'aggiornamento all'ultima versione di Azure PowerShell usando le istruzioni riportate nella [guida ad Azure PowerShell](/powershell/azure/overview) e quindi eseguire `Login-AzureRmAccount` per creare una connessione con Azure. È inoltre necessario eseguire il commit del codice dell'applicazione in un repository Git locale.

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo aver eseguito lo script di esempio, usare il comando seguente per rimuovere il gruppo di risorse, l'App Web e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Crea un'app Web con il gruppo di risorse e il gruppo del servizio app necessari. Quando la directory corrente contiene un repository Git, aggiungere anche un'istanza remota di `azure`. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../app-service-powershell-samples.md) (Esempi di Azure PowerShell).
