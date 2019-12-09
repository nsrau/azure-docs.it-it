---
title: 'PowerShell: Distribuire dal repository GIT locale'
description: Informazioni su come usare Azure PowerShell per automatizzare la distribuzione e la gestione di Servizio app. Questo esempio illustra come distribuire il codice da un repository GIT locale.
tags: azure-service-management
ms.assetid: 5a927f23-8e70-45fd-9aae-980d4e7a007d
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 7355f2730cc54ca027e20c48e989e9f28dac7487
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74684834"
---
# <a name="create-a-web-app-and-deploy-code-from-a-local-git-repository"></a>Creare un'App Web e distribuire il codice da un archivio Git locale

Questo script di esempio crea un'app Web nel servizio app con le relative risorse correlate e quindi distribuisce il codice dell'app Web da un repository Git locale.

Se necessario, effettuare l'aggiornamento all'ultima versione di Azure PowerShell usando le istruzioni riportate nella [guida ad Azure PowerShell](/powershell/azure/overview) e quindi eseguire `Connect-AzAccount` per creare una connessione con Azure. È inoltre necessario eseguire il commit del codice dell'applicazione in un repository Git locale.

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/deploy-local-git/deploy-local-git.ps1?highlight=1 "Create a web app and deploy code from a local Git repository")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo aver eseguito lo script di esempio, usare il comando seguente per rimuovere il gruppo di risorse, l'App Web e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name $webappname -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crea un'app Web con il gruppo di risorse e il gruppo del servizio app necessari. Quando la directory corrente contiene un repository Git, aggiungere anche un'istanza remota di `azure`. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../samples-powershell.md) (Esempi di Azure PowerShell).
