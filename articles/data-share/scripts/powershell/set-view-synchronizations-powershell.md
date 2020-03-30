---
title: 'Script di PowerShell: Impostare e visualizzare le impostazioni di silcronologia di Azure Data Share. Documenti Microsoft'
description: Questo script di PowerShell imposta e ottiene le impostazioni di sincronizzazione delle condivisioni.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: b8d01a383e816bfc73413d21e9cc8d51d326ab14
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307189"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Usare PowerShell per monitorare l'utilizzo di una condivisione dati inviataUse PowerShell to monitor the usage of a sent data share

Questo script di PowerShell imposta e ottiene le impostazioni di sincronizzazione delle condivisioni.

## <a name="sample-script"></a>Script di esempio


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$settingName = "<Synchronization setting name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new synchronization setting
New-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $settingName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List share synchronization settings
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName 

#Get a specific share synchronization setting
Get-AzDataShareSynchronizationSetting -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName  -ShareName $dataShareName -Name $settingName  
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Nuovo-AzDataShareSynchronizationSetting](/powershell/module/az.datashare/new-azdatasharesynchronizationsetting?view=azps-2.6.0) | Creare una sincronizzazione di condivisione. |
| [Get-AzDataShareSynchronizationSetting](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting?view=azps-2.6.0) | Ottiene le impostazioni di sincronizzazione di una sincronizzazione di condivisione. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script di PowerShell per la condivisione dati di Azure sono disponibili negli esempi di [Azure Data Share powerShell.Additional Azure Data Share PowerShell](../../samples-powershell.md)script samples can be found in the Azure Data Share PowerShell samples.
