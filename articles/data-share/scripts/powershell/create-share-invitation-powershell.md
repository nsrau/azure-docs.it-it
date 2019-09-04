---
title: 'Script di PowerShell: Creare un invito di condivisione dati di Azure | Microsoft Docs'
description: Questo script di PowerShell Invia un invito alla condivisione dati.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: b1150d3a60e4ea7c22de5cfe7f9ea4a584c315d9
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70242970"
---
# <a name="use-powershell-to-monitor-the-usage-of-a-sent-data-share"></a>Usare PowerShell per monitorare l'uso di una condivisione dati inviata

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
| [New-AzDataShareInvitation](/powershell/module/az.resources/get-azdatasharesynchronizationdetails) | Creare un invito per la condivisione di dati. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script di PowerShell per la condivisione di dati di Azure sono disponibili negli esempi di PowerShell per la [condivisione di dati di Azure](../../samples-powershell.md).
