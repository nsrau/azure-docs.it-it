---
title: 'Script di PowerShell: creare una nuova condivisione di dati di Azure'
description: Questo script di PowerShell crea una nuova condivisione dati all'interno di un account di condivisione dati esistente.
author: joannapea
ms.service: data-share
ms.topic: article
ms.date: 07/07/2019
ms.author: joanpo
ms.openlocfilehash: 36c1bdfef2afe4c34796a804784317a5e7fe12ff
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92221350"
---
# <a name="use-powershell-to-create-a-data-share-in-azure"></a>Usare PowerShell per creare una condivisione di dati in Azure

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
| [New-AzDataShare](/powershell/module/az.datashare/new-azdatashare) | Crea una condivisione di dati. |
|||

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/).

Altri esempi di script di PowerShell per la condivisione di dati di Azure sono disponibili negli esempi di PowerShell per la [condivisione di dati di Azure](../../samples-powershell.md).
