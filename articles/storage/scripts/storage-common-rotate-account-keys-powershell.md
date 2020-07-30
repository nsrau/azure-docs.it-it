---
title: Eseguire la rotazione delle chiavi di accesso dell'account di archiviazione con PowerShell
titleSuffix: Azure Storage
description: Creare un account di Archiviazione di Azure e recuperare e ruotare una delle relative chiavi di accesso.
services: storage
author: tamram
ms.service: storage
ms.subservice: blobs
ms.devlang: powershell
ms.topic: sample
ms.date: 12/04/2019
ms.author: tamram
ms.openlocfilehash: d532782847ec09d2a480951ecd77ae7746b000c2
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87089371"
---
# <a name="rotate-storage-account-access-keys-with-powershell"></a>Eseguire la rotazione delle chiavi di accesso dell'account di archiviazione con PowerShell

Lo script crea un account di Archiviazione di Azure, consente di visualizzare la chiave di accesso primaria del nuovo account di archiviazione e rinnova, ovvero ruota, la chiave.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione

Eseguire il comando seguente per rimuovere il gruppo di risorse, l'account di archiviazione e tutte le risorse correlate.

```powershell
Remove-AzResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Spiegazione dello script

Lo script usa i comandi seguenti per creare l'account di archiviazione e recuperare e ruotare una delle relative chiavi di accesso. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzLocation](/powershell/module/az.resources/get-azlocation) | Ottiene tutti i percorsi e i provider di risorse supportati per ogni percorso. |
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Crea un gruppo di risorse di Azure. |
| [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) | Crea un account di archiviazione. |
| [Get-AzStorageAccountKey](/powershell/module/az.storage/get-azstorageaccountkey) | Ottiene le chiavi di accesso per l'account di Archiviazione di Azure. |
| [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) | Rigenera una chiave di accesso per l'account di Archiviazione di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/).

Sono disponibili altri esempi di script di archiviazione di PowerShell in [PowerShell samples for Azure Blob storage](../blobs/storage-samples-blobs-powershell.md) (Esempi di PowerShell per l'Archiviazione BLOB di Azure).
