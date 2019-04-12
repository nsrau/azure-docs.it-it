---
title: Esempio di script di Azure PowerShell - Creare un'area di lavoro Log Analytics| Microsoft Docs
description: Esempio di script di Azure PowerShell - Creare un'area di lavoro Log Analytics
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
tags: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: sample
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/07/2017
ms.author: magoedte
ms.openlocfilehash: 4dc98fc3912f9d9c659ecfcaa3df253d30bfcc78
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57306970"
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

