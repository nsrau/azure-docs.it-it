---
title: Calcolare le dimensioni di un contenitore BLOB con PowerShell
titleSuffix: Azure Storage
description: Calcolare le dimensioni di un contenitore dell'Archiviazione BLOB di Azure sommando le dimensioni di ognuno dei relativi BLOB.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: c95f66a492f1fdb6f3c0aef6390f0b925dcf6e53
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089372"
---
# <a name="calculate-the-size-of-a-blob-container-with-powershell"></a>Calcolare le dimensioni di un contenitore BLOB con PowerShell

Lo script consente di calcolare le dimensioni di un contenitore dell'Archiviazione BLOB di Azure sommando le dimensioni dei BLOB del contenitore.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Questo script di PowerShell offre una dimensione stimata per il contenitore e non deve essere usato per calcoli di fatturazione. Per uno script che calcoli le dimensioni del contenitore per la fatturazione, vedere [Calcolare le dimensioni di un contenitore di archiviazione BLOB per la fatturazione](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/storage/calculate-container-size/calculate-container-size.ps1 "Calculate container size")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire il comando seguente per rimuovere il gruppo di risorse, il contenitore e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name bloblisttestrg
```

## <a name="script-explanation"></a>Spiegazione dello script

Lo script usa i comandi seguenti per calcolare le dimensioni del contenitore dell'archiviazione BLOB. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) | Ottiene un account di archiviazione specificato o tutti gli account di archiviazione in un gruppo di risorse o nella sottoscrizione. |
| [Get-AzStorageBlob](/powershell/module/az.storage/Get-AzStorageBlob) | Elenca i BLOB di un contenitore. |

## <a name="next-steps"></a>Passaggi successivi

Per uno script che calcoli le dimensioni del contenitore per la fatturazione, vedere [Calcolare le dimensioni di un contenitore di archiviazione BLOB per la fatturazione](../scripts/storage-blobs-container-calculate-billing-size-powershell.md).

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Sono disponibili altri esempi di script di archiviazione di PowerShell in [PowerShell samples for Azure Blob storage](../blobs/storage-samples-blobs-powershell.md) (Esempi di PowerShell per l'Archiviazione BLOB di Azure).
