---
title: Controllare lo stato di crittografia di un BLOB - Archiviazione di AzureCheck the encryption status of a blob - Azure Storage
description: Informazioni su come usare il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per verificare se un determinato BLOB è crittografato. Se un BLOB non è crittografato, scopri come usare AzCopy per forzare la crittografia scaricando e ricaricando il BLOB.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74707586"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Controllare lo stato di crittografia di un BLOBCheck the encryption status of a blob

Ogni BLOB di blocchi, BLOB di accodamento o BLOB di pagine scritto in Archiviazione di Azure dopo il 20 ottobre 2017 viene crittografato con la crittografia di Archiviazione di Azure.Every block blob, append blob, or page blob that was written to Azure Storage after October 20, 2017 is encrypted with Azure Storage encryption. I BLOB creati prima di questa data continuano a essere crittografati da un processo in background.

Questo articolo illustra come determinare se un determinato BLOB è stato crittografato.

## <a name="check-a-blobs-encryption-status"></a>Controllare lo stato di crittografia di un BLOBCheck a blob's encryption status

Usare il portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per determinare se un BLOB viene crittografato senza codice.

### <a name="azure-portal"></a>[Portale di Azure](#tab/portal)

Per usare il portale di Azure per verificare se un BLOB è stato crittografato, eseguire la procedura seguente:To use the Azure portal to check whether a blob has been encrypted, follow these steps:

1. Nel portale di Azure passare all'account di archiviazione.
1. Selezionare **Contenitori** per passare a un elenco di contenitori nell'account.
1. Individuare il BLOB e visualizzarne la scheda **Panoramica.**
1. Visualizzare la proprietà **Server Encrypted.** Se **True**, come illustrato nell'immagine seguente, il BLOB viene crittografato. Si noti che le proprietà del BLOB includono anche la data e l'ora di creazione del BLOB.

    ![Screenshot che mostra come controllare la proprietà Server Encrypted nel portale di Azure](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[Powershell](#tab/powershell)

Per usare PowerShell per verificare se un BLOB è stato crittografato, controllare la proprietà **IsServerEncrypted** del BLOB. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Per determinare quando è stato creato il BLOB, controllare il valore della proprietà **Created:**

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Interfaccia della riga di comando di AzureAzure](#tab/cli)

Per usare l'interfaccia della riga di comando di Azure per verificare se un BLOB è stato crittografato, controllare la proprietà **IsServerEncrypted** del BLOB. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Per determinare quando è stato creato il BLOB, controllare il valore della proprietà **creata.**

---

## <a name="force-encryption-of-a-blob"></a>Forzare la crittografia di un BLOBForce encryption of a blob

Se un BLOB creato prima del 20 ottobre 2017 non è ancora stato crittografato dal processo in background, è possibile forzare la crittografia immediatamente scaricando e ricaricando il BLOB. Un modo semplice per eseguire questa operazione è con AzCopy.A simple way to do this is with AzCopy.

Per scaricare un BLOB nel file system locale con AzCopy, usare la sintassi seguente:To download a blob to your local file system with AzCopy, use the following syntax:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Per ricaricare il BLOB in Archiviazione di Azure con AzCopy, usare la sintassi seguente:To re-upload the blob to Azure Storage to AzCopy, use the following syntax:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Per altre informazioni sull'uso di AzCopy per copiare i dati BLOB, vedere [Trasferire dati con AzCopy e archiviazione BLOB.](../common/storage-use-azcopy-blobs.md)

## <a name="next-steps"></a>Passaggi successivi

[Crittografia di Archiviazione di Azure per i dati inattiviAzure Storage encryption for data at rest](../common/storage-service-encryption.md)
