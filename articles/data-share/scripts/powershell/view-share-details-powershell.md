---
title: 'Script di PowerShell: elencare le condivisioni esistenti nella condivisione dati di Azure'
description: Questo script di PowerShell elenca e Visualizza i dettagli delle condivisioni.
services: data-share
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.openlocfilehash: 54b4b6cedc2dc09e4a9d5c4b7b937e80750e17c6
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92220823"
---
# <a name="use-powershell-to-view-the-details-of-a-sent-data-share"></a>Usare PowerShell per visualizzare i dettagli di una condivisione dati inviata

Questo script di PowerShell elenca le condivisioni di dati da un account esistente e ottiene i dettagli di una condivisione specifica.


## <a name="sample-script"></a>Script di esempio

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

#Lists all data shares within an account
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName

#Gets details of a specific data share
Get-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Get-AzDataShare](/powershell/module/az.datashare/get-azdatashare) | Ottiene ed elenca le condivisioni in un account. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).

Altri esempi di script di PowerShell per la condivisione di dati di Azure sono disponibili negli esempi di PowerShell per la [condivisione di dati di Azure](../../samples-powershell.md).
