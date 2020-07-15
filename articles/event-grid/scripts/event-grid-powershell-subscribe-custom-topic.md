---
title: Esempio di script di Azure PowerShell - Sottoscrivere un argomento personalizzato | Microsoft Docs
description: Questo articolo include un esempio di script di Azure PowerShell che illustra come sottoscrivere gli eventi di Griglia di eventi per un argomento personalizzato.
ms.devlang: powershell
ms.topic: sample
ms.date: 07/08/2020
ms.openlocfilehash: 89917554da3d5b338ae17332ea8847d6313ca534
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86171177"
---
# <a name="subscribe-to-events-for-a-custom-topic-with-powershell"></a>Sottoscrivere eventi per un argomento personalizzato con PowerShell

Lo script crea una sottoscrizione di Griglia di eventi per gli eventi relativi a un argomento personalizzato.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Lo script di esempio di anteprima richiede il modulo Griglia di eventi. Per installarlo, eseguire `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

## <a name="sample-script---stable"></a>Script di esempio - stabile

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic/subscribe-to-custom-topic.ps1 "Subscribe to custom topic")]

## <a name="sample-script---preview-module"></a>Script di esempio: modulo di anteprima

[!INCLUDE [requires-azurerm](../../../includes/requires-azurerm.md)]

[!code-powershell[main](../../../powershell_scripts/event-grid/subscribe-to-custom-topic-preview/subscribe-to-custom-topic-preview.ps1 "Subscribe to custom topic")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa il comando seguente per creare una sottoscrizione a eventi. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzEventGridSubscription](https://docs.microsoft.com/powershell/module/az.eventgrid/new-azeventgridsubscription) | Creare una sottoscrizione di Griglia di eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](../overview.md).
* Per altre informazioni su PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
