---
title: Come usare gli snapshot di condivisione file di Azure (anteprima) | Microsoft Docs
description: "Usare gli snapshot di condivisione file di Azure. Gli snapshot di condivisione file di Azure sono una versione di sola lettura di una condivisione file di Azure eseguita in un determinato momento. Una volta creato uno snapshot di condivisione, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo. Gli snapshot di condivisione consentono di eseguire un backup della condivisione così com'è in un determinato momento."
services: storage
documentationcenter: .net
author: renash
manager: aungoo
editor: tysonn
ms.assetid: edabe3ee-688b-41e0-b34f-613ac9c3fdfd
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2017
ms.author: renash
ms.openlocfilehash: 6643dad5ea3ba703e26f5708cdd2e925f702847f
ms.sourcegitcommit: 51ea178c8205726e8772f8c6f53637b0d43259c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="work-with-azure-file-share-snapshots-preview"></a>Usare gli snapshot di condivisione file di Azure (anteprima)
Gli snapshot di condivisione file di Azure (anteprima) sono una versione di sola lettura di una condivisione file di Azure eseguita in un determinato momento. Una volta creato uno snapshot di condivisione, è possibile leggerlo, copiarlo o eliminarlo, ma non modificarlo. Gli snapshot di condivisione consentono di eseguire un backup della condivisione così com'è in un determinato momento. In questo articolo si apprenderà come creare, gestire ed eliminare gli snapshot di condivisione file di Azure. Per altre informazioni sugli snapshot di condivisione, vedere [Azure Files share snapshot (preview) overview](storage-snapshots-files.md) (Panoramica sugli snapshot di condivisione file di Azure (anteprima)) o le [domande frequenti sugli snapshot](storage-files-faq.md).

## <a name="create-azure-files-share-snapshots"></a>Creare snapshot di condivisione file di Azure

È possibile creare uno snapshot di condivisione tramite il portale, PowerShell, l'interfaccia della riga di comando, REST o qualsiasi Storage SDK. Le sezioni seguenti illustreranno come creare uno snapshot di condivisione usando il portale, l'interfaccia della riga di comando e PowerShell. 

È possibile creare uno snapshot di condivisione di una condivisione file mentre è in uso. Tuttavia, gli snapshot di condivisione consentono di acquisire solo i dati già scritti nella condivisione file di Azure nel momento in cui viene eseguito il comando per lo snapshot di condivisione. Potrebbero rimanere esclusi tutti i dati memorizzati nella cache da eventuali applicazioni o dal sistema operativo.

### <a name="create-share-snapshot-using-portal"></a>Creare uno snapshot di condivisione tramite il portale  
È sufficiente passare alla condivisione file nel portale e scegliere il pulsante `Create a Snapshot` per creare uno snapshot di condivisione temporizzato.

>   ![./media/storage-snapshots-create/portal-create-snapshot.png](./media/storage-snapshots-create/portal-create-snapshot.png)


### <a name="create-share-snapshot-using-cli-20"></a>Creare uno snapshot di condivisione tramite l'interfaccia della riga di comando 2.0
È possibile creare uno snapshot di condivisione usando il comando `az storage share snapshot`:

```azurecli-interactive
az storage share snapshot -n <share name>
```

Output di esempio
```json
{
  "metadata": {},
  "name": "<share name>",
  "properties": {
    "etag": "\"0x8D50B7F9A8D7F30\"",
    "lastModified": "2017-10-04T23:28:22+00:00",
    "quota": null
  },
  "snapshot": "2017-10-04T23:28:35.0000000Z"
}
```

### <a name="create-share-snapshot-using-powershell"></a>Creare uno snapshot di condivisione tramite PowerShell
È possibile creare uno snapshot di condivisione usando il comando `$share.Snapshot()`:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

```
## <a name="list-share-snapshots-browse-share-snapshot-contents-and-restore-from-snapshots"></a>Elencare gli snapshot di condivisione, esplorare il contenuto degli snapshot di condivisione ed eseguire il ripristino dagli snapshot

È possibile enumerare gli snapshot di condivisione associati a una condivisione file usando l'integrazione “Versioni precedenti” in Windows, tramite REST, la libreria client, PowerShell e il portale. Una volta montata la condivisione file di Azure, è possibile visualizzare tutte le versioni precedenti del file usando l'integrazione “Versioni precedenti” di SMB. Una volta montata la condivisione file di Azure, è possibile visualizzare tutte le versioni precedenti della directory usando l'integrazione “Versioni precedenti” di SMB. Nelle sezioni seguenti si apprenderà come usare il portale di Azure, Windows e l'interfaccia della riga di comando di Azure 2.0 per elencare, esplorare il contenuto e ripristinare snapshot di condivisione.

### <a name="share-snapshot-operations-in-portal"></a>Condividere le operazioni di creazione snapshot nel portale

È possibile esaminare tutti gli snapshot di condivisione per una condivisione file nel portale ed esplorare uno specifico snapshot di condivisione per visualizzarne il contenuto.

#### <a name="view-share-snapshot"></a>Visualizzare uno snapshot di condivisione
Nella condivisione file in Snapshot selezionare **Visualizza snapshot**.

![./media/storage-snapshots-list-browse/snapshot-view-portal.png](./media/storage-snapshots-list-browse/snapshot-view-portal.png)

#### <a name="list-and-browse-share-snapshot-content"></a>Elencare ed esplorare il contenuto di uno snapshot di condivisione
Visualizzare l'elenco degli snapshot di condivisione e quindi visualizzarne direttamente il contenuto selezionando lo snapshot di condivisione dal timestamp desiderato.

![./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png](./media/storage-snapshots-list-browse/snapshot-browsefiles-portal.png)

È anche possibile scegliere il pulsante **Connetti** nella visualizzazione dell'elenco degli snapshot per ottenere il comando `net use` e il percorso di directory di uno snapshot di condivisione specifico, che è possibile esplorare direttamente.


![./media/storage-snapshots-list-browse/snapshot-connect-portal.pngsnapshot-list-portal.png](./media/storage-snapshots-list-browse/snapshot-connect-portal.png)

#### <a name="download-or-restore-from-share-snapshot"></a>Scaricare o eseguire il ripristino da uno snapshot di condivisione
Dal portale, scaricare o ripristinare il file desiderato da uno snapshot.

![./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png](./media/storage-snapshots-list-browse/snapshot-download-restore-portal.png)

### <a name="file-share-snapshot-operations-in-windows"></a>Operazioni di creazione snapshot di condivisione file in Windows
Una volta creati gli snapshot di condivisione della condivisione file, è possibile visualizzare le versioni precedenti di una condivisione, una directory o un determinato file dalla condivisione file di Azure montata in Windows. Di seguito viene illustrato come usare la funzionalità **Versioni precedenti** per visualizzare e ripristinare una versione precedente di una determinata directory in Windows.

> [!Note]  
> Le stesse operazioni possono essere eseguite a livello di condivisione e a livello di file. L'elenco visualizza solo le versioni che contengono modifiche per la specifica directory o il file. Se una particolare directory o un determinato file non è stato modificato tra due snapshot di condivisione, lo snapshot di condivisione verrà riportato solo nell'elenco delle versioni precedenti a livello di condivisione, ma non in quello della directory o del file.

#### <a name="mount-file-share"></a>Montare la condivisione file
Per prima cosa montare la condivisione file usando il comando net use.

#### <a name="open-mounted-share-in-explorer"></a>Aprire la condivisione montata in Esplora file
Passare a Esplora file e individuare la condivisione montata.

![./media/storage-snapshots-list-browse/snapshot-windows-mount.png](./media/storage-snapshots-list-browse/snapshot-windows-mount.png)

#### <a name="list-previous-versions"></a>Elencare le versioni precedenti
 Passare all'elemento o all'elemento padre da ripristinare. Fare doppio clic per passare alla directory desiderata. Fare clic con il pulsante destro del mouse e scegliere "Proprietà" dal menu.

![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png)

Selezionare “**Versioni precedenti”** per visualizzare l'elenco di snapshot di condivisione per questa directory. Il caricamento dell'elenco potrebbe richiedere alcuni secondi in base alla velocità della rete e al numero di snapshot di condivisione.

 ![./media/storage-snapshots-list-browse/snapshot-windows-list.png](./media/storage-snapshots-list-browse/snapshot-windows-list.png)

È possibile selezionare **Apri** per individuare un determinato snapshot. 

 ![./media/storage-snapshots-list-browse/snapshot-browse-windows.png](./media/storage-snapshots-list-browse/snapshot-browse-windows.png)

#### <a name="restore-from-a-previous-version"></a>Eseguire il ripristino da una versione precedente
**Eseguire il ripristino** per copiare il contenuto dell'intera directory in modo ricorsivo al momento della creazione dello snapshot di condivisione nel percorso originale.
 ![./media/storage-snapshots-list-browse/snapshot-windows-previous-versions.png](./media/storage-snapshots-list-browse/snapshot-windows-restore.png)

### <a name="file-share-snapshot-operations-in-azure-cli-20"></a>Operazioni di creazione snapshot di condivisione file nell'interfaccia della riga di comando di Azure 2.0
È possibile usare l'interfaccia della riga di comando di Azure 2.0 per eseguire operazioni come la visualizzazione di un elenco di snapshot di condivisione, l'esplorazione del contenuto di uno snapshot di condivisione, il ripristino o il download di file da uno snapshot di condivisione o l'eliminazione di uno snapshot di condivisione.

#### <a name="list-share-snapshots"></a>Elencare gli snapshot di condivisione

È possibile elencare gli snapshot di condivisione di una specifica condivisione usando [`az storage share list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list) con `--include-snapshots`.

```azurecli-interactive 
az storage share list --include-snapshots
```

#### <a name="sample-output"></a>Output di esempio
Il comando consentirà di visualizzare un elenco di snapshot di condivisione insieme a tutte le relative proprietà associate.

```json
[
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:44:13.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": "2017-10-04T19:45:18.0000000Z"
  },
  {
    "metadata": null,
    "name": "sharesnapshotdefs",
    "properties": {
      "etag": "\"0x8D50B5F4005C975\"",
      "lastModified": "2017-10-04T19:36:46+00:00",
      "quota": 5120
    },
    "snapshot": null
  }
]
```

#### <a name="browse-share-snapshots"></a>Esplorare gli snapshot di condivisione
È inoltre possibile esplorare un determinato snapshot di condivisione per visualizzarne il contenuto usando [`az storage file list`](/cli/azure/storage/share?view=azure-cli-latest#az_storage_share_list). È necessario specificare il nome condivisione `--share-name` e il timestamp da esplorare in `--snapshot '2017-10-04T19:45:18.0000000Z'`.

```azurecli-interactive 
az storage file list --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z' -otable
```

#### <a name="sample-output"></a>Output di esempio

Si noterà che il contenuto dello snapshot di condivisione è identico a quello della condivisione nel momento in cui è stato creato lo snapshot di condivisione.

```
Name            Content Length    Type    Last Modified
--------------  ----------------  ------  ---------------
HelloWorldDir/                    dir
IMG_0966.JPG    533568            file
IMG_1105.JPG    717711            file
IMG_1341.JPG    608459            file
IMG_1405.JPG    652156            file
IMG_1611.JPG    442671            file
IMG_1634.JPG    1495999           file
IMG_1635.JPG    974058            file

```
#### <a name="restore-from-share-snapshots"></a>Eseguire il ripristino da snapshot di condivisione

È possibile ripristinare un file copiandolo o scaricandolo dallo snapshot di condivisione usando il comando `az storage file download`.

```azurecli-interactive 
az storage file download --path IMG_0966.JPG --share-name sharesnapshotdefs --snapshot '2017-10-04T19:45:18.0000000Z'
```

#### <a name="sample-output"></a>Output di esempio

Si noterà che il contenuto del file scaricato e le relative proprietà sono identici al contenuto e alle proprietà nel momento in cui è stato creato lo snapshot di condivisione.

```json
{
  "content": null,
  "metadata": {},
  "name": "IMG_0966.JPG",
  "properties": {
    "contentLength": 533568,
    "contentRange": "bytes 0-533567/533568",
    "contentSettings": {
      "cacheControl": null,
      "contentDisposition": null,
      "contentEncoding": null,
      "contentLanguage": null,
      "contentType": "application/octet-stream"
    },
    "copy": {
      "completionTime": null,
      "id": null,
      "progress": null,
      "source": null,
      "status": null,
      "statusDescription": null
    },
    "etag": "\"0x8D50B5F49F7ACDF\"",
    "lastModified": "2017-10-04T19:37:03+00:00",
    "serverEncrypted": true
  }
}
```

## <a name="delete-azure-files-share-snapshot"></a>Eliminare uno snapshot di condivisione file di Azure

È possibile eliminare snapshot di condivisione file usando il portale di Azure, PowerShell, l'interfaccia della riga di comando, l'API REST o qualsiasi Storage SDK. Le sezioni seguenti illustreranno come eliminare snapshot di condivisione usando il portale di Azure, l'interfaccia della riga di comando e PowerShell.

È possibile esplorare snapshot di condivisione e visualizzare le differenze tra due snapshot di condivisione usando qualsiasi strumento di confronto per determinare gli snapshot di condivisione da eliminare. 

Non è possibile eliminare una condivisione che ha uno snapshot di condivisione. È necessario innanzitutto eliminare tutti i relativi snapshot di condivisione per poter eliminare la condivisione.

### <a name="delete-share-snapshot-using-portal"></a>Eliminare uno snapshot di condivisione tramite il portale  
È possibile passare al pannello della condivisione file e selezionare il pulsante `delete` nel portale per eliminare uno o più snapshot di condivisione.

>   ![./media/storage-snapshots-delete/portal-snapshots-delete.png](./media/storage-snapshots-delete/portal-snapshots-delete.png)


### <a name="delete-a-share-snapshot-using-azure-cli-20"></a>Eliminare uno snapshot di condivisione tramite l'interfaccia della riga di comando di Azure 2.0
È possibile eliminare uno snapshot di condivisione tramite il comando `[az storage share delete]` fornendo il parametro `--snapshot '2017-10-04T23:28:35.0000000Z' ` con il timestamp dello snapshot di condivisione:

```azurecli-interactive
az storage share delete -n <share name> --snapshot '2017-10-04T23:28:35.0000000Z' 
```

Output di esempio
```json
{
  "deleted": true
}
```

### <a name="delete-a-share-snapshot-using-powershell"></a>Eliminare uno snapshot di condivisione tramite PowerShell
È possibile eliminare uno snapshot di condivisione usando il comando `Remove-AzureStorageShare -Share`:

```powershell
$connectionstring="DefaultEndpointsProtocol=http;FileEndpoint=http:<Storage Account Name>.file.core.windows.net /;AccountName=:<Storage Account Name>;AccountKey=:<Storage Account Key>"
$sharename=":<file share name>"

$ctx = New-AzureStorageContext -ConnectionString $connectionstring

##create snapshot
$share=Get-AzureStorageShare -Context $ctx -Name <file share name>
$share.Properties.LastModified
$share.IsSnapshot
$snapshot=$share.Snapshot()

##Delete snapshot
Remove-AzureStorageShare -Share $snapshot

```

## <a name="next-steps"></a>Passaggi successivi
* [Azure Files share snapshot (preview) overview](storage-snapshots-files.md) (Panoramica sugli snapshot di condivisione file di Azure (anteprima))
* [Domande frequenti sugli snapshot](storage-files-faq.md)