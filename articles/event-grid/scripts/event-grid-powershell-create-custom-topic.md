---
title: Esempio di script di Azure PowerShell - Creare un argomento personalizzato | Microsoft Docs
description: Esempio di script di Azure PowerShell - Creare un argomento personalizzato
services: event-grid
documentationcenter: na
author: tfitzmac
manager: timlt
ms.service: event-grid
ms.devlang: powershell
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2018
ms.author: tomfitz
ms.openlocfilehash: 2948f1eabe93d092d905965429ff73eaa6c443cd
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2018
ms.locfileid: "31426074"
---
# <a name="create-event-grid-custom-topic-with-powershell"></a>Creare un argomento personalizzato di Griglia di eventi con PowerShell

Questo script crea un argomento personalizzato di Griglia di eventi.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/event-grid/create-custom-topic/create-custom-topic.ps1 "Create custom topic")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa il comando seguente per creare l'argomento personalizzato. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [New-AzureRmEventGridTopic](https://docs.microsoft.com/powershell/module/azurerm.eventgrid/new-azurermeventgridtopic) | Creare un argomento personalizzato di Griglia di eventi. |

## <a name="next-steps"></a>Passaggi successivi

* Per un'introduzione alle applicazioni gestite, vedere [Panoramica di Applicazione gestita di Azure](../overview.md).
* Per altre informazioni su PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).
