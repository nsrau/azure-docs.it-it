---
title: Esempio di script di Azure PowerShell - Instradare il traffico per la disponibilità elevata delle applicazioni | Microsoft Docs
description: Esempio di script di Azure PowerShell - Instradare il traffico per la disponibilità elevata delle applicazioni
services: traffic-manager
documentationcenter: traffic-manager
author: asudbring
manager: KumudD
ms.service: traffic-manager
ms.devlang: powershell
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: traffic-manager
ms.date: 05/16/2017
ms.author: allensu
ms.openlocfilehash: 2f3b4ef9abdfae2c0400f54a90694db468470c94
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889486"
---
# <a name="route-traffic-for-high-availability-of-applications"></a>Instradare il traffico per la disponibilità elevata delle applicazioni

In questo script viene creato un gruppo di risorse, due piani di servizio app, due app Web, un profilo di gestione traffico e due endpoint di gestione traffico. Gestione traffico indirizza il traffico verso l'applicazione in un'area come area primaria e nell'area secondaria quando l'applicazione nell'area primaria non è disponibile. Prima di eseguire lo script, è necessario modificare i valori MyWebApp, MyWebAppL1 e MyWebAppL2 in valori univoci in Azure. Dopo aver eseguito lo script, è possibile accedere all'app nell'area primaria con l'URL mywebapp.trafficmanager.net.

Se necessario, installare Azure PowerShell usando l'istruzione presente nella [Guida di Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) e quindi eseguire `Connect-AzAccount` per creare una connessione con Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/traffic-manager/direct-traffic-for-increased-application-availability/direct-traffic-for-increased-application-availability.ps1 "Route traffic for high availability")]


Eseguire questo comando per rimuovere il gruppo di risorse, la macchina virtuale e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name myResourceGroup1
Remove-AzResourceGroup -Name myResourceGroup2
```


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare un gruppo di risorse, un'App Web, un profilo di Gestione traffico e tutte le risorse correlate. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)  | Consente di creare un gruppo di risorse in cui sono archiviate tutte le risorse. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Consente di creare un piano di servizio app. Equivale a una server farm per l'App Web di Azure. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Consente di creare un'App Web di Azure all'interno del piano di servizio app. |
| [Set-AzResource](/powershell/module/az.resources/new-azresource) | Consente di creare un'App Web di Azure all'interno del piano di servizio app. |
| [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) | Crea un profilo di Gestione traffico di Azure. |
| [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) | Aggiunge un endpoint a un profilo di Gestione traffico di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview).

Altri esempi di script di PowerShell per la rete sono disponibili nella [documentazione con la panoramica delle reti di Azure](../powershell-samples.md?toc=%2fazure%2fnetworking%2ftoc.json).
