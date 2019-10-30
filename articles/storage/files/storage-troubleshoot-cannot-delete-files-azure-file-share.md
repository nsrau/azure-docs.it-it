---
title: 'Condivisione file di Azure: non è stato possibile eliminare i file dalla condivisione file di Azure'
description: Identificare e risolvere i problemi relativi all'impossibilità di eliminare file dalla condivisione file di Azure.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
ms.date: 10/25/2019
ms.service: storage
ms.subservice: common
ms.openlocfilehash: b535578328e7ca77f1071187b6ac761bc7076ac1
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/30/2019
ms.locfileid: "73065977"
---
# <a name="azure-file-share--failed-to-delete-files-from-azure-file-share"></a>Condivisione file di Azure: non è stato possibile eliminare i file dalla condivisione file di Azure

La mancata eliminazione dei file dalla condivisione file di Azure può presentare diversi sintomi:

**Sintomo 1:**

Non è stato possibile eliminare un file nella condivisione file di Azure a causa di uno dei due problemi seguenti:

* File contrassegnato per l'eliminazione
* La risorsa specificata può essere usata da un client SMB

**Sintomo 2:**

Quota insufficiente per l'elaborazione del comando

## <a name="cause"></a>Causa

L'errore 1816 si verifica quando si raggiunge il limite massimo di handle aperti simultanei consentiti per un file, nel computer in cui viene montata la condivisione file. Per altre informazioni, vedere [elenco di controllo delle prestazioni e della scalabilità di archiviazione di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-performance-checklist).

## <a name="resolution"></a>Risoluzione

Ridurre il numero di handle aperti simultanei chiudendo alcuni handle.

## <a name="prerequisite"></a>Prerequisito

### <a name="install-the-latest-azure-powershell-module"></a>Installare il modulo Azure PowerShell più recente

* [Installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps)

### <a name="connect-to-azure"></a>Connettersi ad Azure:

```
# Connect-AzAccount
```

### <a name="select-the-subscription-of-the-target-storage-account"></a>Selezionare la sottoscrizione dell'account di archiviazione di destinazione:

```
# Select-AzSubscription -subscriptionid "SubscriptionID"
```

### <a name="create-context-for-the-target-storage-account"></a>Creare il contesto per l'account di archiviazione di destinazione:

```
$Context = New-AzStorageContext -StorageAccountName "StorageAccountName" -StorageAccountKey "StorageAccessKey"
```

### <a name="get-the-current-open-handles-of-the-file-share"></a>Ottenere gli handle aperti correnti della condivisione file:

```
# Get-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Recursive
```

## <a name="example-result"></a>Risultato di esempio:

|HandleId|path|clientIp|clientPort|OpenTime|LastReconnectTime|FileId|ParentId|SessionId|
|---|---|---|---|---|---|---|---|---|
|259101229083|---|10.222.10.123|62758|2019-10-05|12:16:50Z|0|0|9507758546259807489|
|259101229131|---|10.222.10.123|62758|2019-10-05|12:36:20Z|0|0|9507758546259807489|
|259101229137|---|10.222.10.123|62758|2019-10-05|12:36:53Z|0|0|9507758546259807489|
|259101229136|Nuova cartella/test. zip|10.222.10.123|62758|2019-10-05|12:36:29Z|13835132822072852480|9223446803645464576|9507758546259807489|
|259101229135|test. zip|37.222.22.143|62758|2019-10-05|12:36:24Z|11529250230440558592|0|9507758546259807489|

### <a name="close-an-open-handle"></a>Chiudere un handle aperto:

Per chiudere un handle aperto, utilizzare il comando seguente:

```
# Close-AzStorageFileHandle -Context $Context -ShareName "FileShareName" -Path 'New folder/test.zip' -CloseAll
```

## <a name="next-steps"></a>Passaggi successivi

* [Risolvere i problemi relativi a File di Azure in Windows](storage-troubleshoot-windows-file-connection-problems.md)
* [Risolvere i problemi relativi a File di Azure in Linux](storage-troubleshoot-linux-file-connection-problems.md)
* [Risolvere i problemi di Sincronizzazione file di Azure](storage-sync-files-troubleshoot.md)