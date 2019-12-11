---
title: Usare PowerShell per file & ACL in Azure Data Lake Storage Gen2 (anteprima)
description: Usare i cmdlet di PowerShell per gestire directory e elenchi di controllo di accesso (ACL) di file e directory negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 11/24/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: f2a2eaa3224fff117a30dfb742b4f8a35196dba4
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973901"
---
# <a name="use-powershell-for-files--acls-in-azure-data-lake-storage-gen2-preview"></a>Usare PowerShell per file & ACL in Azure Data Lake Storage Gen2 (anteprima)

Questo articolo illustra come usare PowerShell per creare e gestire directory, file e autorizzazioni negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS). 

> [!IMPORTANT]
> Il modulo PowerShell disponibile in questo articolo è attualmente disponibile in anteprima pubblica.

 | [mapping da Gen1 a Gen2 per](#gen1-gen2-map) [inviare commenti e suggerimenti](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico (HNS) abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne uno.
> * .NET Framework è installato 4.7.2 o versione successiva. Vedere [scaricare .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * PowerShell versione `5.1` o successiva.

## <a name="install-powershell-modules"></a>Installare i moduli di PowerShell

1. Verificare che la versione di PowerShell installata sia `5.1` o superiore usando il comando seguente. 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    Per aggiornare la versione di PowerShell, vedere [aggiornamento di Windows PowerShell esistente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Installare il modulo **PowershellGet** più recente. Chiudere e riaprire la console di PowerShell.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  Installare il modulo **AZ. storage** Preview.

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    Per altre informazioni su come installare i moduli di PowerShell, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Connettersi all'account

1. Aprire una finestra di comando di Windows PowerShell.

2. Accedere alla sottoscrizione di Azure con il comando `Connect-AzAccount` e seguire le istruzioni visualizzate.

   ```powershell
   Connect-AzAccount
   ```

3. Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione in cui si vogliono creare e gestire le directory.

   ```powershell
   Select-AzSubscription -SubscriptionId <subscription-id>
   ```

   Sostituire il valore del segnaposto `<subscription-id>` con l'ID della sottoscrizione.

4. Ottenere l'account di archiviazione.

   ```powershell
   $storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
   ```

   * Sostituire il valore del segnaposto `<resource-group-name>` con il nome del gruppo di risorse.

   * Sostituire il valore segnaposto `<storage-account-name>` con il nome del proprio account di archiviazione.

5. Ottenere il contesto dell'account di archiviazione.

   ```powershell
   $ctx = $storageAccount.Context
   ```

## <a name="create-a-file-system"></a>Creare un file system

Un file system funge da contenitore per i file. È possibile crearne uno usando il cmdlet `New-AzDatalakeGen2FileSystem`. 

In questo esempio viene creato un file system denominato `my-file-system`.

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory usando il cmdlet `New-AzDataLakeGen2Item`. 

In questo esempio viene aggiunta una directory denominata `my-directory` a un file system.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Questo esempio aggiunge la stessa directory, ma imposta anche le autorizzazioni, umask, i valori delle proprietà e i valori dei metadati. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Mostra proprietà Directory

In questo esempio viene ottenuta una directory utilizzando il cmdlet `Get-AzDataLakeGen2Item`, quindi i valori delle proprietà vengono stampati nella console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Directory.PathProperties.Group
$dir.Directory.PathProperties.Owner
$dir.Directory.Metadata
$dir.Directory.Properties
```

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory utilizzando il cmdlet `Move-AzDataLakeGen2Item`.

Questo esempio rinomina una directory dal nome `my-directory` al nome `my-new-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

In questo esempio una directory denominata `my-directory` viene spostata in una sottodirectory di `my-directory-2` denominata `my-subdirectory`. Questo esempio applica anche un umask alla sottodirectory.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory utilizzando il cmdlet `Remove-AzDataLakeGen2Item`.

In questo esempio viene eliminata una directory denominata `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

È possibile usare il parametro `-Force` per rimuovere il file senza una richiesta.

## <a name="download-from-a-directory"></a>Scarica da una directory

Scaricare un file da una directory utilizzando il cmdlet `Get-AzDataLakeGen2ItemContent`.

Questo esempio Scarica un file denominato `upload.txt` da una directory denominata `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto di una directory utilizzando il cmdlet `Get-AzDataLakeGen2ChildItem`.

Questo esempio elenca il contenuto di una directory denominata `my-directory`. 

Per elencare il contenuto di un file system, omettere il parametro `-Path` dal comando.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

Questo esempio elenca il contenuto di una directory denominata `my-directory` e include gli ACL nell'elenco. USA anche il parametro `-Recurse` per elencare il contenuto di tutte le sottodirectory.

Per elencare il contenuto di un file system, omettere il parametro `-Path` dal comando.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
```

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Caricare un file in una directory utilizzando il cmdlet `New-AzDataLakeGen2Item`.

In questo esempio viene caricato un file denominato `upload.txt` in una directory denominata `my-directory`. 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

In questo esempio viene caricato lo stesso file, ma vengono quindi impostati le autorizzazioni, umask, i valori delle proprietà e i valori dei metadati del file di destinazione. Questo esempio visualizza anche questi valori nella console.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>Mostra proprietà file

Questo esempio Mostra come ottenere un file usando il cmdlet `Get-AzDataLakeGen2Item`, quindi i valori delle proprietà vengono stampati nella console.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.File.PathProperties.Group
$file.File.PathProperties.Owner
$file.File.Metadata
$file.File.Properties
```

## <a name="delete-a-file"></a>Eliminare un file

Eliminare un file usando il cmdlet `Remove-AzDataLakeGen2Item`.

In questo esempio viene eliminato un file denominato `upload.txt`. 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

È possibile usare il parametro `-Force` per rimuovere il file senza una richiesta.

## <a name="manage-access-permissions"></a>Gestire le autorizzazioni di accesso

È possibile ottenere, impostare e aggiornare le autorizzazioni di accesso di directory e file.

### <a name="get-directory-and-file-permissions"></a>Ottenere le autorizzazioni per directory e file

Ottenere l'ACL di una directory o di un file usando il cmdlet `Get-AzDataLakeGen2Item`.

Questo esempio Mostra come ottenere l'ACL di una **directory**e quindi stampa l'ACL nella console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

Questo esempio Mostra come ottenere l'ACL di un **file** e quindi stamparlo nella console.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Nell'immagine seguente viene illustrato l'output dopo aver ricevuto l'ACL di una directory.

![Ottieni output ACL](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

In questo esempio, l'utente proprietario dispone delle autorizzazioni di lettura, scrittura ed esecuzione. Il gruppo proprietario dispone solo delle autorizzazioni di lettura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [controllo di accesso in Azure Data Lake storage Gen2](data-lake-storage-access-control.md).

### <a name="set-directory-and-file-permissions"></a>Impostare le autorizzazioni per directory e file

Usare il cmdlet `New-AzDataLakeGen2ItemAclObject` per creare un ACL per l'utente proprietario, il gruppo proprietario o altri utenti. Usare quindi il cmdlet `Update-AzDataLakeGen2Item` per eseguire il commit dell'ACL.

Questo esempio imposta l'ACL in una **directory** per l'utente proprietario, il gruppo proprietario o altri utenti, quindi stampa l'ACL nella console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
In questo esempio viene impostato l'ACL in un **file** per l'utente proprietario, il gruppo proprietario o altri utenti, quindi viene stampato l'ACL nella console.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Nell'immagine seguente viene illustrato l'output dopo l'impostazione dell'ACL di un file.

![Ottieni output ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

In questo esempio, l'utente proprietario e il gruppo proprietario hanno solo le autorizzazioni di lettura e scrittura. Tutti gli altri utenti dispongono di autorizzazioni di scrittura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [controllo di accesso in Azure Data Lake storage Gen2](data-lake-storage-access-control.md).

### <a name="update-directory-and-file-permissions"></a>Aggiornare le autorizzazioni per directory e file

Usare il cmdlet `Get-AzDataLakeGen2Item` per ottenere l'ACL di una directory o di un file. Usare quindi il cmdlet `New-AzDataLakeGen2ItemAclObject` per creare una nuova voce ACL. Usare il cmdlet `Update-AzDataLakeGen2Item` per applicare il nuovo ACL.

In questo esempio viene assegnata all'utente un'autorizzazione di scrittura ed esecuzione per una directory.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```
In questo esempio viene assegnata all'utente un'autorizzazione di scrittura ed esecuzione su un file.

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityId $id -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $acl
```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>Impostare le autorizzazioni per tutti gli elementi di un file system

È possibile utilizzare il `Get-AzDataLakeGen2Item` e il parametro `-Recurse` insieme al cmdlet `Update-AzDataLakeGen2Item` per impostare in modo ricorsivo l'ACL di tutte le directory e i file in un file system. 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Mapping tra Gen1 e Gen2

Nella tabella seguente viene illustrato il modo in cui i cmdlet utilizzati per Data Lake Storage Gen1 vengono mappati ai cmdlet di per Data Lake Storage Gen2.

|Cmdlet Data Lake Storage Gen1| Cmdlet Data Lake Storage Gen2| Note |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Per impostazione predefinita, il cmdlet Get-AzDataLakeGen2ChildItem elenca solo gli elementi figlio di primo livello. Il parametro-Recurse elenca gli elementi figlio in modo ricorsivo. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Gli elementi di output del cmdlet Get-AzDataLakeGen2Item dispongono di queste proprietà: ACL, Owner, Group, Permission.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Il cmdlet Get-AzDataLakeGen2FileContent Scarica il contenuto del file nel file locale.|
|Move-AzDataLakeStoreItem|Move-AzDataLakeGen2Item||
|New-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Questo cmdlet carica il nuovo contenuto del file da un file locale.|
|Remove-AzDataLakeStoreItem|Remove-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Update-AzDataLakeGen2Item|Il cmdlet Update-AzDataLakeGen2Item aggiorna un solo elemento e non in modo ricorsivo. Se si vuole aggiornare in modo ricorsivo, elencare gli elementi usando il cmdlet Get-AzDataLakeStoreChildItem, quindi eseguire la pipeline al cmdlet Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Il cmdlet Get-AzDataLakeGen2Item segnalerà un errore se l'elemento non esiste.|



## <a name="see-also"></a>Vedi anche

* [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Uso di Azure PowerShell con Archiviazione di Azure](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Cmdlet di PowerShell](/powershell/module/az.storage)per l'archiviazione.

