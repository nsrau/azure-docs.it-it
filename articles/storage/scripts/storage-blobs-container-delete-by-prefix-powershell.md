---
title: Esempio di script di Azure PowerShell - Eliminare i contenitori mediante prefisso | Microsoft Docs
description: Eliminare i contenitori BLOB dell'Archiviazione di Azure in base a un prefisso del nome.
services: storage
documentationcenter: na
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 06/13/2017
ms.author: robinsh
ms.openlocfilehash: 402958c4e2978630bc79557704a77e77a8b9a7e7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="delete-containers-based-on-container-name-prefix"></a>Eliminare i contenitori in base al prefisso del nome

Lo script elimina i contenitori nell'Archiviazione BLOB di Azure in base a un prefisso nel nome del contenitore.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/storage/delete-containers-by-prefix/delete-containers-by-prefix.ps1 "Delete containers by prefix")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire il comando seguente per rimuovere il gruppo di risorse, i contenitori rimanenti e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name containerdeletetestrg
```

## <a name="script-explanation"></a>Spiegazione dello script

Lo script usa i comandi seguenti per eliminare i contenitori in base al prefisso contenuto nel nome dei contenitori stessi. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzureRMStorageAccount](/powershell/module/azurerm.storage/get-azurermstorageaccount) | Ottiene un account di archiviazione specificato o tutti gli account di archiviazione in un gruppo di risorse o nella sottoscrizione. |
| [Get-AzureStorageContainer](/powershell/module/azure.storage/get-azurestoragecontainer) | Elenca i contenitori di archiviazione associati a un account di archiviazione. |
| [Remove-AzureStorageContainer](/powershell/module/azure.storage/remove-azurestoragecontainer) | Elimina il contenitore di archiviazione specificato. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Sono disponibili altri esempi di script di archiviazione di PowerShell in [PowerShell samples for Azure Blob storage](../blobs/storage-samples-blobs-powershell.md) (Esempi di PowerShell per l'Archiviazione BLOB di Azure).
