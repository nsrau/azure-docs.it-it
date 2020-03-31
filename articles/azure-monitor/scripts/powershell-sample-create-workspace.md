---
title: Creare un'area di lavoro Log Analytics - Azure PowerShell
description: Esempio di script di Azure PowerShell - Creare un'area di lavoro Log Analytics
ms.subservice: logs
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 09/07/2017
ms.openlocfilehash: 62b02de5d1c08f6047052d71e3be420cceb1c5c0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/24/2020
ms.locfileid: "80054638"
---
# <a name="create-a-log-analytics-workspace-with-powershell"></a>Creare un'area di lavoro Log Analytics con PowerShell

Questo script consente all'utente di apprendere e usare rapidamente un'area di lavoro Azure Log Analytics, un passo necessario se si desidera iniziare a raccogliere, analizzare o intraprendere un'azione sui dati.  

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-powershell[main](../../../powershell_scripts/log-analytics/log-analytics-create-new-resource/log-analytics-create-new-resource.ps1 "Create new Log Analytics workspace")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per creare una nuova area di lavoro Log Analytics nella sottoscrizione. Ogni comando della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/get-azoperationalinsightsworkspace) | Consente di ottenere informazioni su un'area di lavoro esistente. |
| [New-AzOperationalInsightsWorkspace](/powershell/module/az.operationalinsights/new-azoperationalinsightsworkspace) | Consente di creare un'area di lavoro nel gruppo di risorse e nel percorso specificati. |


## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

