---
title: Esempio di script di Azure PowerShell - Creare un'area di lavoro Log Analytics| Microsoft Docs
description: Esempio di script di Azure PowerShell - Creare un'area di lavoro Log Analytics
ms.service: azure-monitor
ms.subservice: logs
ms.topic: sample
author: MGoedtel
ms.author: magoedte
ms.date: 09/07/2017
ms.openlocfilehash: 6aad6c6f0656bd317a808de13e340d5774cea49b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72931903"
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

