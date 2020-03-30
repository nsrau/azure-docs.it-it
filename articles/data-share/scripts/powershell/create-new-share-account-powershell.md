---
title: 'Script di PowerShell: Creare un nuovo account di condivisione dati di Azure Documenti Microsoft'
description: Questo script di PowerShell crea un nuovo account di condivisione dati.
services: data-share
author: joannapea
ms.service: data-share
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: c3852dd5f1d3d3df8a982716ce5dab9426782869
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70307262"
---
# <a name="use-powershell-to-create-a-data-share-account-in-azure"></a>Usare PowerShell per creare un account di condivisione dati in AzureUse PowerShell to create a data share account in Azure

Questo script di PowerShell crea un nuovo account di condivisione dati. 

## <a name="sample-script"></a>Script di esempio

```powershell
# Set variables with your own values
$resourceGroupName = "<Resource group name"
$location = "<Location>"
$dataShareAccountName = "<Data share account name>"

# Create a new Azure Data Share account
New-AzDataShareAccount -ResourceGroupName $resourceGroupName -Name $dataShareAccountName -Location $location
```

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti: 

| Comando | Note |
|---|---|
| [Nuovo-AzDataShareAccount](/powershell/module/az.datashare/new-azdatashareaccount?view=azps-2.6.0) | Crea un account di condivisione dati. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](https://docs.microsoft.com/powershell/).

Altri esempi di script di PowerShell per la condivisione dati di Azure sono disponibili negli esempi di [Azure Data Share powerShell.Additional Azure Data Share PowerShell](../../samples-powershell.md)script samples can be found in the Azure Data Share PowerShell samples.
