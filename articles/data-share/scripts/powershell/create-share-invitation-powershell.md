---
title: 'Script di PowerShell: creare un invito alla condivisione dati di Azure Documenti Microsoft'
description: Questo script di PowerShell invia un invito alla condivisione dati.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 9fd8d6428e94007002d524d9ade99f6b368b8201
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307239"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Usare PowerShell per monitorare l'utilizzo di una condivisione dati inviataUse PowerShell to monitor the usage of a sent data share

Questo script di PowerShell crea un invito alla condivisione dati.

## <a name="sample-script"></a>Script di esempio


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$targetEmail = "<Target email>"

# Send a data share invitation
New-AzDataShareInvitation -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -Name $dataShareName -TargetEmail $targetEmail

```


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Invito New-AzDataShareInvitation](/powershell/module/az.datashare/new-azdatashareinvitation?view=azps-2.6.0) | Creare un invito alla condivisione dati. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script di PowerShell per la condivisione dati di Azure sono disponibili negli esempi di [Azure Data Share powerShell.Additional Azure Data Share PowerShell](../../samples-powershell.md)script samples can be found in the Azure Data Share PowerShell samples.
