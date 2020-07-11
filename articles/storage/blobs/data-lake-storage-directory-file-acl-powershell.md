---
title: Azure Data Lake Storage Gen2 PowerShell per file & ACL
description: Usare i cmdlet di PowerShell per gestire directory e elenchi di controllo di accesso (ACL) di file e directory negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS).
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: how-to
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: d22b83e1f3464f6d87d2bc3821682b25e05d947b
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86142544"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Usare PowerShell per gestire directory, file e ACL in Azure Data Lake Storage Gen2

Questo articolo illustra come usare PowerShell per creare e gestire directory, file e autorizzazioni negli account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico (HNS). 

[Mapping da Gen1 a Gen2 ](#gen1-gen2-map) | [Fornire commenti e suggerimenti](https://github.com/Azure/azure-powershell/issues)

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione in cui è abilitato lo spazio dei nomi gerarchico. Per crearne uno, seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni.
> * .NET Framework è installato 4.7.2 o versione successiva. Vedere [scaricare .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Versione di PowerShell `5.1` o successiva.

## <a name="install-the-powershell-module"></a>Installare il modulo PowerShell

1. Verificare che la versione di PowerShell installata sia `5.1` o superiore usando il comando seguente.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Per aggiornare la versione di PowerShell, vedere [aggiornamento di Windows PowerShell esistente](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Installare **AZ. storage** Module.

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
   ```

   Per altre informazioni su come installare i moduli di PowerShell, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)

## <a name="connect-to-the-account"></a>Effettuare la connessione all'account

Aprire una finestra di comando di Windows PowerShell e accedere alla sottoscrizione di Azure con il `Connect-AzAccount` comando e seguire le istruzioni visualizzate.

```powershell
Connect-AzAccount
```

Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione in cui si vogliono creare e gestire le directory. In questo esempio, sostituire il `<subscription-id>` valore del segnaposto con l'ID della sottoscrizione.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Scegliere quindi come si vuole che i comandi ottengano l'autorizzazione per l'account di archiviazione. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opzione 1: ottenere l'autorizzazione utilizzando Azure Active Directory (AD)

Con questo approccio, il sistema garantisce che l'account utente disponga delle assegnazioni di controllo degli accessi in base al ruolo (RBAC) e delle autorizzazioni ACL appropriate. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opzione 2: ottenere l'autorizzazione usando la chiave dell'account di archiviazione

Con questo approccio, il sistema non controlla le autorizzazioni RBAC o ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-container"></a>Creare un contenitore

Un contenitore funge da file system per i file. È possibile crearne uno usando il `New-AzStorageContainer` cmdlet. 

Questo esempio crea un contenitore denominato `my-file-system` .

```powershell
$filesystemName = "my-file-system"
New-AzStorageContainer -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla directory usando il `New-AzDataLakeGen2Item` cmdlet. 

In questo esempio viene aggiunta una directory denominata `my-directory` a un contenitore.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

Questo esempio aggiunge la stessa directory, ma imposta anche le autorizzazioni, umask, i valori delle proprietà e i valori dei metadati. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Mostrare le proprietà della directory

In questo esempio viene ottenuta una directory utilizzando il `Get-AzDataLakeGen2Item` cmdlet, quindi i valori delle proprietà vengono stampati nella console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir =  Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
$dir.Permissions
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
```

## <a name="rename-or-move-a-directory"></a>Rinominare o spostare una directory

Rinominare o spostare una directory utilizzando il `Move-AzDataLakeGen2Item` cmdlet.

Questo esempio rinomina una directory dal nome `my-directory` con il nome `my-new-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

> [!NOTE]
> Utilizzare il `-Force` parametro se si desidera sovrascrivere senza prompt.

In questo esempio viene spostata una directory denominata `my-directory` in una sottodirectory di `my-directory-2` denominata `my-subdirectory` . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory utilizzando il `Remove-AzDataLakeGen2Item` cmdlet.

Questo esempio illustra come eliminare una directory denominata `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

È possibile usare il `-Force` parametro per rimuovere il file senza una richiesta.

## <a name="download-from-a-directory"></a>Scaricare da una directory

Scaricare un file da una directory utilizzando il `Get-AzDataLakeGen2ItemContent` cmdlet.

Questo esempio illustra come scaricare un file denominato `upload.txt` da una directory denominata `my-directory`. 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto di una directory utilizzando il `Get-AzDataLakeGen2ChildItem` cmdlet. È possibile usare il parametro facoltativo `-OutputUserPrincipalName` per ottenere il nome, anziché l'ID oggetto, degli utenti.

Questo esempio elenca il contenuto di una directory denominata `my-directory` .

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

Nell'esempio seguente vengono elencate `ACL` le `Permissions` proprietà,, `Group` e `Owner` di ogni elemento nella directory. Il `-FetchProperty` parametro è necessario per ottenere i valori per la `ACL` Proprietà. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Per elencare il contenuto di un contenitore, omettere il `-Path` parametro dal comando.

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Caricare un file in una directory utilizzando il `New-AzDataLakeGen2Item` cmdlet.

Questo esempio illustra come caricare un file denominato `upload.txt` in una directory denominata `my-directory`. 

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
$file1.Properties
$file1.Properties.Metadata

```

## <a name="show-file-properties"></a>Mostrare le proprietà file

Questo esempio Mostra come ottenere un file usando il `Get-AzDataLakeGen2Item` cmdlet, quindi i valori delle proprietà vengono stampati nella console.

```powershell
$filepath =  "my-directory/upload.txt"
$filesystemName = "my-file-system"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filepath
$file
$file.ACL
$file.Permissions
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
```

## <a name="delete-a-file"></a>Eliminare un file

Eliminare un file usando il `Remove-AzDataLakeGen2Item` cmdlet.

In questo esempio viene eliminato un file denominato `upload.txt` . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

È possibile usare il `-Force` parametro per rimuovere il file senza una richiesta.

## <a name="manage-access-permissions"></a>Gestire le autorizzazioni di accesso

È possibile ottenere, impostare e aggiornare le autorizzazioni di accesso di file e directory. Queste autorizzazioni vengono acquisite in elenchi di controllo di accesso (ACL).

> [!NOTE]
> Se si usa Azure Active Directory per autorizzare i comandi, assicurarsi che all'entità di sicurezza sia stato assegnato il [ruolo proprietario del BLOB di archiviazione](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner). Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control).

### <a name="get-an-acl"></a>Recuperare un ACL

Ottenere l'ACL di una directory o di un file usando il `Get-AzDataLakeGen2Item` cmdlet.

Questo esempio Mostra come ottenere l'ACL della directory radice di un **contenitore** e quindi stampare l'ACL sulla console.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

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

Nell'immagine seguente illustra l'output dopo aver recuperato l'ACL di una directory.

![Recuperare l'output di un ACL](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

In questo esempio, l'utente proprietario è provvisto delle autorizzazioni di lettura, scrittura ed esecuzione. Il gruppo proprietario è provvisto delle sole autorizzazioni di lettura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).

### <a name="set-an-acl"></a>Impostare un ACL

Usare il `set-AzDataLakeGen2ItemAclObject` cmdlet per creare un ACL per l'utente proprietario, il gruppo proprietario o altri utenti. Usare quindi il `Update-AzDataLakeGen2Item` cmdlet per eseguire il commit dell'ACL.

Questo esempio imposta l'ACL nella directory radice di un **contenitore** per l'utente proprietario, il gruppo proprietario o altri utenti, quindi stampa l'ACL nella console.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

Questo esempio imposta l'ACL in una **directory** per l'utente proprietario, il gruppo proprietario o altri utenti, quindi stampa l'ACL nella console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```
In questo esempio viene impostato l'ACL in un **file** per l'utente proprietario, il gruppo proprietario o altri utenti, quindi viene stampato l'ACL nella console.

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath -Acl $acl
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

Nell'immagine seguente illustra l'output dopo aver impostato l'ACL di un file.

![Recuperare l'output di un ACL](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

In questo esempio, l'utente proprietario e il gruppo proprietario sono provvisti delle sole autorizzazioni di lettura e scrittura. Tutti gli altri utenti sono provvisti di autorizzazioni di scrittura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Controllo di accesso in Azure Data Lake Storage Gen2](data-lake-storage-access-control.md).


### <a name="set-acls-on-all-items-in-a-container"></a>Impostare gli ACL per tutti gli elementi in un contenitore

È possibile utilizzare il `Get-AzDataLakeGen2Item` parametro e il `-Recurse` parametro insieme al `Update-AzDataLakeGen2Item` cmdlet per impostare in modo ricorsivo l'ACL per le directory e i file in un contenitore. 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl

$Token = $Null
do
{
     $items = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse -ContinuationToken $Token    
     if($items.Count -le 0) { Break;}
     $items | Update-AzDataLakeGen2Item -Acl $acl
     $Token = $items[$items.Count -1].ContinuationToken;
}
While ($Token -ne $Null) 
```

### <a name="add-or-update-an-acl-entry"></a>Aggiungi o aggiorna una voce ACL

Per prima cosa, ottenere l'ACL. Usare quindi il `set-AzDataLakeGen2ItemAclObject` cmdlet per aggiungere o aggiornare una voce ACL. Usare il `Update-AzDataLakeGen2Item` cmdlet per eseguire il commit dell'ACL.

In questo esempio viene creato o aggiornato l'ACL in una **directory** per un utente.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -EntityID xxxxxxxx-xxxx-xxxxxxxxxxx -Permission r-x -InputObject $acl 
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $acl
```

### <a name="remove-an-acl-entry"></a>Rimuovere una voce ACL

In questo esempio viene rimossa una voce da un ACL esistente.

```powershell
$id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "User"-and $a.DefaultScope -eq $false -and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew
```

<a id="gen1-gen2-map"></a>

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
* [Cmdlet di PowerShell per l'archiviazione](/powershell/module/az.storage)
