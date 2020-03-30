---
title: 'Condivisione file di Azure: impossibile eliminare i file dalla condivisione file di Azure'
description: Identificare e risolvere i problemi relativi all'errore di eliminazione dei file dalla condivisione file di Azure.Identify and troubleshoot the failure to delete files from Azure File Share.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: d3a3763a8964810626bcdc47da230a9ee406f1f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74196488"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Condivisione file di Azure: impossibile eliminare i file dalla condivisione file di Azure

L'errore di eliminazione di file da Condivisione file di Azure può presentare diversi sintomi:The failure to delete files from Azure File Share can have several symptoms:

**Sintomo 1:**

Impossibile eliminare un file nella condivisione file di Azure a causa di uno dei due problemi seguenti:

* Il file contrassegnato per l'eliminazione
* La risorsa specificata può essere utilizzata da un client SMB

**Sintomo 2:**

Non c'è abbastanza disponibilità per elaborare il comando

## <a name="cause"></a>Causa

L'errore 1816 si verifica quando si raggiunge il limite superiore di handle aperti simultanei consentiti per un file, nel computer in cui viene montata la condivisione file. Per altre informazioni, vedere l'elenco di controllo per le prestazioni e la scalabilità di Archiviazione di Azure.For more information, see the [Azure Storage performance and scalability checklist](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist).

## <a name="resolution"></a>Risoluzione

Ridurre il numero di handle aperti simultanei chiudendo alcuni handle.

## <a name="prerequisite"></a>Prerequisito

### <a name="install-the-latest-azure-powershell-module"></a>Installare il modulo di Azure PowerShell più recenteInstall the latest Azure PowerShell module

* [Installare il modulo di Azure PowerShellInstall the Azure PowerShell module](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Connettersi ad Azure:Connect to Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Selezionare la sottoscrizione dell'account di archiviazione di destinazione:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Creare il contesto per l'account di archiviazione di destinazione:Create context for the target storage account:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Ottenere gli handle aperti correnti della condivisione file:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Risultato di esempio:Example result:

|HandleId|Path|ClientIp|ClientPort (Porta client)|OpenTime (in openTime)|Tempo LastReconnect|FileId|Parentid|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53|0|0|9507758546259807489|
|259101229136|Nuova cartella/test.zip|10.222.10.123|62758|2019-10-05|12:36:29|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test.zip|37.222.22.143|62758|2019-10-05|12:36:24|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Chiudere una maniglia aperta:

Per chiudere un handle aperto, utilizzare il comando seguente:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi relativi a File di Azure in Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Risolvere i problemi relativi a File di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md)