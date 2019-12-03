---
title: Verificare lo stato della crittografia di un BLOB-archiviazione di Azure
description: Informazioni su come usare portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per verificare se un determinato BLOB è crittografato. Se un BLOB non è crittografato, informazioni su come usare AzCopy per forzare la crittografia scaricando e ricaricando il BLOB.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 5cef0e94a43b3ef16d45f7f43658f962e07b5345
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707586"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Verificare lo stato della crittografia di un BLOB

Ogni BLOB in blocchi, BLOB di accodamento o BLOB di pagine che è stato scritto in archiviazione di Azure dopo il 20 ottobre 2017 è crittografato con la crittografia di archiviazione di Azure. I BLOB creati prima di questa data continuano a essere crittografati da un processo in background.

Questo articolo illustra come determinare se un determinato BLOB è stato crittografato.

## <a name="check-a-blobs-encryption-status"></a>Verificare lo stato di crittografia di un BLOB

Usare la portale di Azure, PowerShell o l'interfaccia della riga di comando di Azure per determinare se un BLOB è crittografato senza codice.

### <a name="azure-portaltabportal"></a>[Azure portal](#tab/portal)

Per usare il portale di Azure per verificare se un BLOB è stato crittografato, attenersi alla procedura seguente:

1. Nel portale di Azure passare all'account di archiviazione.
1. Selezionare **contenitori** per passare a un elenco di contenitori nell'account.
1. Individuare il BLOB e visualizzarne la scheda **Panoramica** .
1. Visualizzare la proprietà **crittografata del server** . Se **true**, come illustrato nell'immagine seguente, il BLOB è crittografato. Si noti che le proprietà del BLOB includono anche la data e l'ora di creazione del BLOB.

    ![Screenshot che illustra come controllare la proprietà crittografata del server in portale di Azure](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

Per usare PowerShell per verificare se un BLOB è stato crittografato, controllare la proprietà **IsServerEncrypted** del BLOB. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Per determinare quando è stato creato il BLOB, controllare il valore della proprietà **creata** :

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-clitabcli"></a>[interfaccia della riga di comando di Azure](#tab/cli)

Per usare l'interfaccia della riga di comando di Azure per verificare se un BLOB è stato crittografato, controllare la proprietà **IsServerEncrypted** del BLOB. È necessario ricordare di sostituire i valori segnaposto tra parentesi uncinate con i valori personalizzati:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Per determinare quando è stato creato il BLOB, controllare il valore della proprietà **creata** .

---

## <a name="force-encryption-of-a-blob"></a>Forzare la crittografia di un BLOB

Se un blob creato prima del 20 ottobre 2017 non è ancora stato crittografato dal processo in background, è possibile forzare immediatamente la crittografia scaricando e ricaricando il BLOB. Un modo semplice per eseguire questa operazione è con AzCopy.

Per scaricare un BLOB nel file system locale con AzCopy, usare la sintassi seguente:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Per caricare nuovamente il BLOB in archiviazione di Azure con AzCopy, usare la sintassi seguente:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Per altre informazioni sull'uso di AzCopy per copiare i dati BLOB, vedere [trasferire dati con AzCopy e archiviazione BLOB](../common/storage-use-azcopy-blobs.md).

## <a name="next-steps"></a>Passaggi successivi

[Crittografia di archiviazione di Azure per dati inattivi](../common/storage-service-encryption.md)
