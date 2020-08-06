---
title: Azure PowerShell - Sottoscrivere un gruppo di risorse
description: Questo articolo include un esempio di script di Azure PowerShell che illustra come sottoscrivere gli eventi di Griglia di eventi per un gruppo di risorse e come applicare filtri per trovare risorse.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: f3aeec208ba6b80643e50307b89f590977bfb446
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87460763"
---
# <a name="subscribe-to-events-for-a-resource-group-and-filter-for-a-resource-with-powershell"></a>Sottoscrivere eventi per un gruppo di risorse e applicare un filtro per una risorsa con PowerShell

Lo script crea una sottoscrizione di Griglia di eventi per gli eventi relativi a un gruppo di risorse. Usa un filtro per ottenere solo gli eventi per una risorsa specifica nel gruppo di risorse.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Script di esempio - stabile

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events/filter-events.ps1 "Filter events")]

## <a name="sample-script---preview-module"></a>Script di esempio: modulo di anteprima

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

Lo script di esempio di anteprima richiede il modulo Griglia di eventi. Per installarlo, eseguire `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

[!code-powershell[main](../../../powershell_scripts/event-grid/filter-events-preview/filter-events-preview.ps1 "Filter events")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa il comando seguente per creare una sottoscrizione a eventi. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzEventGridSubscription](/powershell/module/az.eventgrid/new-azeventgridsubscription) | Creare una sottoscrizione di Griglia di eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](../overview.md).
* Per altre informazioni su PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/get-started-azureps).
