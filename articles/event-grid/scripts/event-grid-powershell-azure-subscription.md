---
title: Azure PowerShell - Sottoscrivere una sottoscrizione di Azure
description: Questo articolo include un esempio di script di Azure PowerShell che illustra come sottoscrivere gli eventi di Griglia di eventi per una sottoscrizione di Azure.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 7540c8eb181e064c5df5372a4bc9b27ebe16bf56
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171245"
---
# <a name="subscribe-to-events-for-an-azure-subscription-with-powershell"></a>Sottoscrivere eventi per una sottoscrizione di Azure con PowerShell

Lo script crea una sottoscrizione di Griglia di eventi per gli eventi relativi a una sottoscrizione di Azure.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script---stable"></a>Script di esempio - stabile

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription/subscribe-to-azure-subscription.ps1 "Subscribe to Azure subscription")]

## <a name="sample-script---preview-module"></a>Script di esempio: modulo di anteprima

Questo script di esempio in anteprima richiede il modulo Griglia di eventi. Per installarlo, eseguire `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-azure-subscription-preview/subscribe-to-azure-subscription-preview.ps1 "Subscribe to Azure subscription")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa il comando seguente per creare una sottoscrizione a eventi. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Creare una sottoscrizione di Griglia di eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](../overview.md).
* Per altre informazioni su PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
