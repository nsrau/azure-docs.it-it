---
title: Esempio di script di Azure PowerShell - Ruotare la chiave di accesso dell'account di archiviazione | Microsoft Docs
description: Creare un account di Archiviazione di Azure e recuperare e ruotare una delle relative chiavi di accesso.
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
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: fe7e12991cb59eb32e173b0552eb155bf66c6c76
ms.contentlocale: it-it
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-storage-account-and-rotate-its-account-access-keys"></a>Creare un account di archiviazione e ruotare le relative chiavi di accesso

Lo script crea un account di Archiviazione di Azure, consente di visualizzare la chiave di accesso primaria del nuovo account di archiviazione e rinnova, ovvero ruota, la chiave.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/storage/rotate-storage-account-keys/rotate-storage-account-keys.ps1 "Rotate storage account keys")]

## <a name="clean-up-deployment"></a>Pulire la distribuzione 

Eseguire il comando seguente per rimuovere il gruppo di risorse, l'account di archiviazione e tutte le risorse correlate.

```powershell
Remove-AzureRmResourceGroup -Name rotatekeystestrg
```

## <a name="script-explanation"></a>Spiegazione dello script

Lo script usa i comandi seguenti per creare l'account di archiviazione e recuperare e ruotare una delle relative chiavi di accesso. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation) | Ottiene tutti i percorsi e i provider di risorse supportati per ogni percorso. |
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crea un gruppo di risorse di Azure. |
| [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount) | Crea un account di archiviazione. |
| [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) | Ottiene le chiavi di accesso per l'account di Archiviazione di Azure. |
| [New-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/new-azurermstorageaccountkey) | Rigenera una chiave di accesso per l'account di Archiviazione di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Sono disponibili altri esempi di script di archiviazione di PowerShell in [PowerShell samples for Azure Blob storage](../blobs/storage-samples-blobs-powershell.md) (Esempi di PowerShell per l'Archiviazione BLOB di Azure).

