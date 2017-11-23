---
title: Esempio di script di Azure PowerShell - Calcolare le dimensioni del contenitore BLOB | Microsoft Docs
description: Calcolare le dimensioni di un contenitore dell'Archiviazione BLOB di Azure sommando le dimensioni di ognuno dei relativi BLOB.
services: storage
documentationcenter: na
author: tamram
manager: jeconnoc
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: sample
ms.date: 11/07/2017
ms.author: tamram
ms.openlocfilehash: f6f421e780bfbb7922a4b11f758330f2a9a0b84b
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2017
---
# <a name="calculate-the-size-of-a-blob-storage-container"></a>Calcolare le dimensioni di un contenitore di archiviazione BLOB

Lo script consente di calcolare le dimensioni di un contenitore dell'Archiviazione BLOB di Azure sommando le dimensioni dei BLOB del contenitore.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Questo script di PowerShell offre una dimensione stimata per il contenitore e non deve essere usato per calcoli di fatturazione. Per uno script che calcoli le dimensioni del contenitore per la fatturazione, vedere [Calcolare le dimensioni di un contenitore di archiviazione BLOB per la fatturazione](../scripts/storage-blobs-container-calculate-billing-size-powershell.md). 

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire il comando seguente per rimuovere il gruppo di risorse, il contenitore e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Spiegazione dello script

Lo script usa i comandi seguenti per calcolare le dimensioni del contenitore dell'archiviazione BLOB. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzureRMStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Ottiene un account di archiviazione specificato o tutti gli account di archiviazione in un gruppo di risorse o nella sottoscrizione. |
| [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) | Elenca i BLOB di un contenitore. ||

## <a name="next-steps"></a>Passaggi successivi

Per uno script che calcoli le dimensioni del contenitore per la fatturazione, vedere [Calcolare le dimensioni di un contenitore di archiviazione BLOB per la fatturazione](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Sono disponibili altri esempi di script di archiviazione di PowerShell in [PowerShell samples for Azure Blob storage](../blobs/storage-samples-blobs-powershell.md) (Esempi di PowerShell per l'Archiviazione BLOB di Azure).
