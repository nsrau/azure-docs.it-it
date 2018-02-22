---
title: Esempio di script di Azure PowerShell - Eseguire la migrazione di oggetti BLOB tra account di archiviazione tramite AzCopy in Windows | Microsoft Docs
description: Tramite AzCopy copia il contenuto degli oggetti BLOB da un account di Archiviazione di Azure a un altro.
services: storage
documentationcenter: na
author: roygara
manager: jeconnoc
ms.custom: mvc
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/01/2018
ms.author: rogarana
ms.openlocfilehash: 970315c5d597d691454f9dea0a76f2c0dc4a40ec
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/03/2018
---
# <a name="migrate-blobs-across-storage-accounts-using-azcopy-on-windows"></a>Eseguire la migrazione di oggetti BLOB tra account di archiviazione tramite AzCopy in Windows

In questo esempio vengono copiati tutti gli oggetti BLOB da un account di archiviazione di origine fornito dall'utente a un account di archiviazione di destinazione fornito dall'utente. 

Questa operazione viene eseguita mediante il comando `Get-AzureStorageContainer`, che elenca tutti i contenitori di un account di archiviazione. L'esempio genera quindi i comandi AzCopy, che eseguono la copia di ogni contenitore dall'account di archiviazione di origine all'account di archiviazione di destinazione. Se si verificano problemi, l'esempio esegue il numero di tentativi definito dal parametro $retryTimes (l'impostazione predefinita è 3 ma il valore può essere modificato mediante il parametro `-RetryTimes`). Se l'errore si verifica a ogni nuovo tentativo, l'utente può rieseguire lo script fornendo il codice di esempio con l'ultimo contenitore copiato correttamente tramite il parametro `-LastSuccessContainerName`. L'esempio continua quindi la copia dei contenitori da tale punto.

Per questo esempio è necessario disporre del modulo di archiviazione di Azure PowerShell **4.0.2** o versioni successive. È possibile controllare la versione installata tramite `Get-Module -ListAvailable Azure.storage`. Se è necessario eseguire l'installazione o l'aggiornamento, vedere come [installare il modulo Azure PowerShell](/powershell/azure/install-azurerm-ps). 

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

Questo esempio richiede inoltre la versione più recente di[AzCopy in Windows](http://aka.ms/downloadazcopy). La directory di installazione predefinita è `C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\`.

Questo esempio usa un nome e una chiave dell'account di archiviazione di origine, un nome e una chiave dell'account di archiviazione di destinazione e il percorso intero di AzCopy.exe (se non è installato nella directory predefinita).

Di seguito sono riportati alcuni esempi di input per questo esempio:

Se AzCopy è installato nella directory predefinita:
```PowerShell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
```

Se AzCopy non è installato nella directory predefinita:

```Powershell
srcStorageAccountName: ExampleSourceStorageAccountName
srcStorageAccountKey: ExampleSourceStorageAccountKey
DestStorageAccountName: ExampleTargetStorageAccountName
DestStorageAccountKey: ExampleTargetStorageAccountKey
AzCopyPath: C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\AzCopy.exe
```

Se si verifica un errore e se è necessario rieseguire l'esempio da un contenitore specifico: 

`.\copyScript.ps1 -LastSuccessContainerName myContainerName`

## <a name="sample-script"></a>Script di esempio

[!code-powershell[main](../../../powershell_scripts/storage/migrate-blobs-between-accounts/migrate-blobs-between-accounts.ps1 "Migrate blobs between storage accounts.")]

## <a name="script-explanation"></a>Spiegazione dello script

Questo script usa i comandi seguenti per copiare i dati da un account di archiviazione a un altro. Ogni elemento della tabella include collegamenti alla documentazione specifica del comando.

| Comando | Note |
|---|---|
| [Get-AzureStorageContainer](/powershell/module/azure.storage/Get-AzureStorageContainer) | Restituisce i contenitori associati all'account di archiviazione corrente. |
| [New-AzureStorageContext](/powershell/module/azure.storage/New-AzureStorageContext) | Crea un contesto di Archiviazione di Azure. |

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul modulo Azure PowerShell, vedere la [documentazione di Azure PowerShell](/powershell/azure/overview).

Sono disponibili altri esempi di script di archiviazione di PowerShell in [PowerShell samples for Azure Blob storage](../blobs/storage-samples-blobs-powershell.md) (Esempi di PowerShell per l'Archiviazione BLOB di Azure).
