---
title: 'Script di PowerShell: creare e visualizzare i trigger di uno snapshot di condivisione dati di AzurePowerShell script: Create and view an Azure Data Share snapshot triggers Documenti Microsoft'
description: Questo script di PowerShell crea e ottiene trigger di snapshot di condivisione.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 4f3148a4d5bd0d39ccfcf7e92e80300a7e19effa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307223"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Usare PowerShell per monitorare l'utilizzo di una condivisione dati inviataUse PowerShell to monitor the usage of a sent data share

Questo script di PowerShell crea e ottiene trigger di snapshot di condivisione.

## <a name="sample-script"></a>Script di esempio

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$subscriptionName = "<Share subscription name>"
$recurrenceInterval = "<Synchronization recurrence interval>"
$synchronizationTime = "<Synchronization time>"

# Create a new snapshot trigger
New-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName -Name $dataShareName  -RecurrenceInterval $recurrenceInterval -SynchronizationTime $synchronizationTime

#List snapshot triggers
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName $subscriptionName  -Name $dataShareName

#Get a specific share snapshot trigger
Get-AzDataShareTrigger -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareSubscriptionName -Name $dataShareName
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Nuovo-AzDataShareTrigger](/powershell/module/az.datashare/new-azdatasharetrigger?view=azps-2.6.0) | Creare un trigger di snapshot di condivisione. |
| [Get-AzDataShareTrigger](/powershell/module/az.datashare/get-azdatasharesynchronizationsetting?view=azps-2.6.0) | Ottiene le impostazioni di sincronizzazione di una sincronizzazione di condivisione. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script di PowerShell per la condivisione dati di Azure sono disponibili negli esempi di [Azure Data Share powerShell.Additional Azure Data Share PowerShell](../../samples-powershell.md)script samples can be found in the Azure Data Share PowerShell samples.
