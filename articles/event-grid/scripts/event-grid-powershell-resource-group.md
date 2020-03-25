---
title: Esempio di script di Azure PowerShell - Sottoscrivere un gruppo di risorse | Microsoft Docs
description: Questo articolo include un esempio di script di Azure PowerShell che illustra come sottoscrivere gli eventi di Griglia di eventi per un gruppo di risorse.
services: event-grid
documentationcenter: na
author: spelluru
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: e285f628d1ec3afce55dc8fe4efa50a361dab5ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "76720810"
---
# <a name="subscribe-to-events-for-a-resource-group-with-powershell"></a>Sottoscrivere eventi per un gruppo di risorse con PowerShell

Lo script crea una sottoscrizione di Griglia di eventi per gli eventi relativi a un gruppo di risorse.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Lo script di esempio di anteprima richiede il modulo Griglia di eventi. Per installarlo, eseguire `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Script di esempio - stabile

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-resource-group/subscribe-to-resource-group.ps1 "Subscribe to resource group")]

## <a name="sample-script---preview-module"></a>Script di esempio: modulo di anteprima

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-resource-group-preview/subscribe-to-resource-group-preview.ps1 "Subscribe to resource group")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa il comando seguente per creare una sottoscrizione a eventi. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Creare una sottoscrizione di Griglia di eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](../overview.md).
* Per altre informazioni su PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
