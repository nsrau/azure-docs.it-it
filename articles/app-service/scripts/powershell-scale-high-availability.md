---
title: 'PowerShell: Ridimensionare con Gestione traffico'
description: Informazioni su come usare Azure PowerShell per automatizzare la distribuzione e la gestione di Servizio app. Questo esempio illustra come ridimensionare un'app a livello globale con Gestione traffico.
tags: azure-service-management
ms.assetid: 470f0129-1efe-462c-a029-5c66e04158a8
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc
ms.openlocfilehash: 6ffcf22b640d6393ed48d803f6f52bac5334d4b7
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74684334"
---
# <a name="scale-a-web-app-worldwide-with-a-high-availability-architecture"></a>Ridimensionare un'App Web a livello globale con un'architettura a disponibilità elevata

In questo scenario verranno creati un gruppo di risorse, due piani di servizio app, due App Web, una profilo di Gestione traffico e due endpoint di endpoint di Gestione traffico. Dopo aver completato l'esercizio si avrà un'architettura a disponibilità elevata che fornisce la disponibilità globale dell'App Web in base alla minima latenza di rete.

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](/powershell/azure/overview) e quindi eseguire `Connect-AzAccount` per creare una connessione con Azure.

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/scale-geographic/scale-geographic.ps1 "Scale a web app worldwide with a high-availability architecture")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Dopo aver eseguito lo script di esempio, usare il comando seguente per rimuovere il gruppo di risorse, l'App Web e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Crea un profilo di Gestione traffico. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Consente di creare un piano di servizio app. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Crea un'App Web. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Crea un endpoint nel profilo di Gestione traffico. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Altri esempi di Azure PowerShell per app Web del servizio app di Azure sono disponibili in [Azure PowerShell samples](../samples-powershell.md) (Esempi di Azure PowerShell).
