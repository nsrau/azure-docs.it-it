---
title: "Script di PowerShell: Monitorare l'utilizzo di una condivisione dati di Azure | Microsoft Docs"
description: Questo script di PowerShell recupera le metriche di utilizzo di una condivisione dati inviata.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 0a4084d309dd0160970f1c03540705b310eb8e75
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307197"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Usare PowerShell per monitorare l'uso di una condivisione dati inviata

Questo script di PowerShell monitora l'utilizzo dei dati elencando le sincronizzazioni di una condivisione dati inviata e ottenendo i dettagli di una sincronizzazione specifica.

## <a name="sample-script"></a>Script di esempio


```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"
$synchronizationId = "<Azure synchronization id>"

# List synchronizations on a share
Get-AzDataShareSynchronization -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName

#Get details of a specific synchronization
Get-AzDataShareSynchronizationDetails -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -ShareName $dataShareName -SynchronizationId $synchronizationId
```


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Get-AzDataShareSynchronization](/powershell/module/az.datashare/get-azdatasharesynchronization?view=azps-2.6.0) | Elencare le sincronizzazioni in una condivisione. |
| [Get-AzDataShareSynchronizationDetails](/powershell/module/az.datashare/get-azdatasharesynchronizationdetail?view=azps-2.6.0) | Ottiene i dettagli della sincronizzazione di una sincronizzazione della condivisione. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script di PowerShell per la condivisione di dati di Azure sono disponibili negli esempi di PowerShell per la [condivisione di dati di Azure](../../samples-powershell.md).
