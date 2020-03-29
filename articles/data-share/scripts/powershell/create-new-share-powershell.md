---
title: 'Script di PowerShell: creare una nuova condivisione dati di Azure . Documenti Microsoft'
description: Questo script di PowerShell crea una nuova condivisione dati all'interno di un account di condivisione dati esistente.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 54d5dc35cf230b62cbe5f8c3cb4a1dc81eccf1e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307255"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Usare PowerShell per creare una condivisione dati in AzureUse PowerShell to create a Data Share in Azure

Questo script di PowerShell crea una nuova condivisione dati all'interno di un account di condivisione dati esistente.

## <a name="sample-script"></a>Script di esempio

```powershell

# Set variables with your own values
$resourceGroupName = "<Resource group name>"
$dataShareAccountName = "<Data share account name>"
$dataShareName = "<Data share name>"

# Create a new Azure Data Share
New-AzDataShare -ResourceGroupName $resourceGroupName -AccountName $dataShareAccountName -Name $dataShareName

```


## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Nuovo-AzDataShare](/powershell/module/az.datashare/new-azdatashare?view=azps-2.6.0) | Crea una condivisione dati. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script di PowerShell per la condivisione dati di Azure sono disponibili negli esempi di [Azure Data Share powerShell.Additional Azure Data Share PowerShell](../../samples-powershell.md)script samples can be found in the Azure Data Share PowerShell samples.
