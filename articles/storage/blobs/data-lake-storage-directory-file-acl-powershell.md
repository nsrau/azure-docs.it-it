---
title: Azure Data Lake Storage Gen2 PowerShell per file & ACL (anteprima)Azure Data Lake Storage Gen2 PowerShell for files & ACLs (preview)
description: Utilizzare i cmdlet di PowerShell per gestire le directory e gli elenchi di controllo di accesso (ACL) di file e directory negli account di archiviazione con spazio dei nomi gerarchico abilitato.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: a2f3dbf58363331cf6b1b05e759d246e68e7e7a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471211"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2-preview"></a>Usare PowerShell per gestire directory, file e ACL in Azure Data Lake Storage Gen2 (anteprima)Use PowerShell to manage directories, files, and ACLs in Azure Data Lake Storage Gen2 (preview)

Questo articolo illustra come usare PowerShell per creare e gestire directory, file e autorizzazioni negli account di archiviazione con spazio dei nomi gerarchico abilitato. 

> [!IMPORTANT]
> Il modulo Di PowerShell presente in questo articolo è attualmente in anteprima pubblica.

[Mapping da Gen1 a Gen2](#gen1-gen2-map) | [Inviare commenti e suggerimentiGen1](https://github.com/Azure/azure-powershell/issues) to Gen2 mapping Give feedback

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne una.
> * .NET Framework è 4.7.2 o versione successiva. Vedere [Scaricare .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Versione `5.1` di PowerShell o versione successiva.

## <a name="install-powershell-modules"></a>Installare i moduli di PowerShell

1. Verificare che la versione di `5.1` PowerShell installata sia o successiva utilizzando il comando seguente. 

    ```powershell
    echo $PSVersionTable.PSVersion.ToString() 
    ```
    
    Per aggiornare la versione di PowerShell, vedere [Upgrading existing Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Installare il modulo **PowershellGet** più recente. Chiudere quindi e riaprire la console di PowerShell.Then, close and reopen the Powershell console.

    ```powershell
    install-Module PowerShellGet –Repository PSGallery –Force 
    ```

3.  Installare il modulo di anteprima **Az.Storage.Install Az.Storage** preview module.

    ```powershell
    install-Module Az.Storage -Repository PSGallery -RequiredVersion 1.9.1-preview –AllowPrerelease –AllowClobber –Force 
    ```

    Per altre informazioni su come installare i moduli di PowerShell, vedere [Installare il modulo di Azure PowerShellFor](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) more information about how to install PowerShell modules, see Install the Azure PowerShell module

## <a name="connect-to-the-account"></a>Connettersi all'account

Aprire una finestra di comando di Windows PowerShell e `Connect-AzAccount` quindi accedere alla sottoscrizione di Azure con il comando e seguire le istruzioni visualizzate.

```powershell
Connect-AzAccount
```

Se l'identità è associata a più di una sottoscrizione, impostare la sottoscrizione attiva sulla sottoscrizione dell'account di archiviazione in cui si vuole creare e gestire le directory. In questo esempio `<subscription-id>` sostituire il valore segnaposto con l'ID della sottoscrizione.

```powershell
Select-AzSubscription -SubscriptionId <subscription-id>
```

Scegliere quindi la modalità di assegnazione dei comandi all'account di archiviazione. 

### <a name="option-1-obtain-authorization-by-using-azure-active-directory-ad"></a>Opzione 1: Ottenere l'autorizzazione tramite Azure Active Directory (AD)Option 1: Obtain authorization by using Azure Active Directory (AD)

Con questo approccio, il sistema garantisce che l'account utente disponga delle assegnazioni di controllo degli accessi in base al ruolo e delle autorizzazioni ACL appropriate. 

```powershell
$ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -UseConnectedAccount
```

### <a name="option-2-obtain-authorization-by-using-the-storage-account-key"></a>Opzione 2: Ottenere l'autorizzazione usando la chiave dell'account di archiviazioneOption 2: Obtain authorization by using the storage account key

Con questo approccio, il sistema non controlla le autorizzazioni RBAC o ACL.

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "<resource-group-name>" -AccountName "<storage-account-name>"
$ctx = $storageAccount.Context
```

## <a name="create-a-file-system"></a>Creare un file system

Un file system funge da contenitore per i file. È possibile crearne `New-AzDatalakeGen2FileSystem` uno utilizzando il cmdlet. 

In questo esempio viene `my-file-system`creato un file system denominato .

```powershell
$filesystemName = "my-file-system"
New-AzDatalakeGen2FileSystem -Context $ctx -Name $filesystemName
```

## <a name="create-a-directory"></a>Creare una directory

Creare un riferimento alla `New-AzDataLakeGen2Item` directory utilizzando il cmdlet. 

In questo esempio `my-directory` viene aggiunta una directory denominata a un file system.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory
```

In questo esempio viene aggiunta la stessa directory, ma vengono impostati anche le autorizzazioni, i valori umask, le proprietà e i valori dei metadati. 

```powershell
$dir = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Directory -Permission rwxrwxrwx -Umask ---rwx---  -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
```

## <a name="show-directory-properties"></a>Mostra proprietà directory

In questo esempio viene `Get-AzDataLakeGen2Item` ottenuta una directory utilizzando il cmdlet e quindi vengono stampati i valori delle proprietà nella console.

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

Rinominare o spostare `Move-AzDataLakeGen2Item` una directory utilizzando il cmdlet.

In questo esempio viene rinominata una directory dal nome `my-directory` al nome `my-new-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-new-directory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -DestFileSystem $filesystemName -DestPath $dirname2
```

In questo esempio `my-directory` viene spostata `my-directory-2` una `my-subdirectory`directory denominata in una sottodirectory denominata . In questo esempio viene inoltre applicata una umask alla sottodirectory.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2 -Umask --------x -PathRenameMode Posix
```

## <a name="delete-a-directory"></a>Eliminare una directory

Eliminare una directory `Remove-AzDataLakeGen2Item` utilizzando il cmdlet.

In questo esempio viene `my-directory`eliminata una directory denominata . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $dirname 
```

È possibile `-Force` utilizzare il parametro per rimuovere il file senza un prompt.

## <a name="download-from-a-directory"></a>Scarica da una directory

Scaricare un file da una `Get-AzDataLakeGen2ItemContent` directory utilizzando il cmdlet.

In questo esempio `upload.txt` viene scaricato `my-directory`un file denominato da una directory denominata . 

```powershell
$filesystemName = "my-file-system"
$filePath = "my-directory/upload.txt"
$downloadFilePath = "download.txt"
Get-AzDataLakeGen2ItemContent -Context $ctx -FileSystem $filesystemName -Path $filePath -Destination $downloadFilePath
```

## <a name="list-directory-contents"></a>Elencare il contenuto della directory

Elencare il contenuto di `Get-AzDataLakeGen2ChildItem` una directory utilizzando il cmdlet.

In questo esempio viene elencato il contenuto di una directory denominata `my-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname
```

In questo esempio non vengono `ACL` `Permissions`restituiti valori per le proprietà , , `Group`e `Owner` . Per ottenere tali valori, utilizzare il `-FetchPermission` parametro . 

Nell'esempio riportato di seguito viene elencato `-FetchPermission` il contenuto della `ACL`stessa `Permissions` `Group`directory, ma viene utilizzato anche il parametro per restituire i valori delle proprietà , , e . `Owner` 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchPermission
$properties.ACL
$properties.Permissions
$properties.Group
$properties.Owner
```

Per elencare il contenuto di un `-Path` file system, omettere il parametro dal comando.

## <a name="upload-a-file-to-a-directory"></a>Caricare un file in una directory

Caricare un file in una `New-AzDataLakeGen2Item` directory utilizzando il cmdlet.

In questo esempio viene `upload.txt` caricato un `my-directory`file denominato in una directory denominata . 

```powershell
$localSrcFile =  "upload.txt"
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$destPath = $dirname + (Get-Item $localSrcFile).Name
New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Force 
```

In questo esempio vengono caricati lo stesso file, ma vengono impostati i valori delle autorizzazioni, umask, delle proprietà e dei metadati del file di destinazione. In questo esempio vengono stampati anche questi valori nella console.

```powershell
$file = New-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $destPath -Source $localSrcFile -Permission rwxrwxrwx -Umask ---rwx--- -Property @{"ContentEncoding" = "UDF8"; "CacheControl" = "READ"} -Metadata  @{"tag1" = "value1"; "tag2" = "value2" }
$file1
$file1.File.Metadata
$file1.File.Properties
```

## <a name="show-file-properties"></a>Mostra proprietà file

In questo esempio viene `Get-AzDataLakeGen2Item` ottenuto un file utilizzando il cmdlet e quindi vengono stampati i valori delle proprietà nella console.

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

Eliminare un file `Remove-AzDataLakeGen2Item` utilizzando il cmdlet.

In questo esempio viene `upload.txt`eliminato un file denominato . 

```powershell
$filesystemName = "my-file-system"
$filepath = "upload.txt"
Remove-AzDataLakeGen2Item  -Context $ctx -FileSystem $filesystemName -Path $filepath 
```

È possibile `-Force` utilizzare il parametro per rimuovere il file senza un prompt.

## <a name="manage-access-permissions"></a>Gestire le autorizzazioni di accesso

È possibile ottenere, impostare e aggiornare le autorizzazioni di accesso di directory e file.

> [!NOTE]
> Se si usa Azure Active Directory (Azure AD) per autorizzare i comandi, assicurarsi che all'entità di sicurezza sia stato assegnato il ruolo Proprietario dati BLOB di [archiviazione.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere Controllo di [accesso in Archiviazione di Azure Data Lake Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

### <a name="get-directory-and-file-permissions"></a>Ottenere le autorizzazioni per directory e fileGet directory and file permissions

Ottenere l'ACL di una directory `Get-AzDataLakeGen2Item`o di un file utilizzando il cmdlet.

In questo esempio viene ottenuto l'ACL di una **directory**, quindi l'ACL viene stampato nella console.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dir = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname
$dir.ACL
```

In questo esempio viene ottenuto l'ACL di un **file** e quindi viene stampato l'ACL nella console.

```powershell
$filePath = "my-directory/upload.txt"
$file = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $filePath
$file.ACL
```

L'immagine seguente mostra l'output dopo aver ottenuto l'ACL di una directory.

![Ottenere l'output ACLGet ACL output](./media/data-lake-storage-directory-file-acl-powershell/get-acl.png)

In questo esempio, l'utente proprietario dispone delle autorizzazioni di lettura, scrittura ed esecuzione. Il gruppo proprietario dispone solo delle autorizzazioni di lettura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Controllo di accesso in Azure Data Lake Storage Gen2.For](data-lake-storage-access-control.md)more information about access control lists, see Access control in Azure Data Lake Storage Gen2 .

### <a name="set-directory-and-file-permissions"></a>Impostare le autorizzazioni per directory e file

Utilizzare `New-AzDataLakeGen2ItemAclObject` il cmdlet per creare un ACL per l'utente proprietario, il gruppo proprietario o altri utenti. Quindi, utilizzare `Update-AzDataLakeGen2Item` il cmdlet per eseguire il commit dell'ACL.

In questo esempio l'ACL viene impostato su una **directory** per l'utente proprietario, il gruppo proprietario o altri utenti, quindi l'ACL viene stampato nella console.

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
In questo esempio viene impostato l'ACL su un **file** per l'utente proprietario, il gruppo proprietario o altri utenti e quindi viene stampato l'ACL nella console.

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

L'immagine seguente mostra l'output dopo aver impostato l'ACL di un file.

![Ottenere l'output ACLGet ACL output](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

In questo esempio, l'utente proprietario e il gruppo proprietario dispongono solo delle autorizzazioni di lettura e scrittura. Tutti gli altri utenti dispongono di autorizzazioni di scrittura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Controllo di accesso in Azure Data Lake Storage Gen2.For](data-lake-storage-access-control.md)more information about access control lists, see Access control in Azure Data Lake Storage Gen2 .

### <a name="update-directory-and-file-permissions"></a>Aggiornare le autorizzazioni per directory e file

Utilizzare `Get-AzDataLakeGen2Item` il cmdlet per ottenere l'ACL di una directory o di un file. Quindi, utilizzare `New-AzDataLakeGen2ItemAclObject` il cmdlet per creare una nuova voce ACL. Utilizzare `Update-AzDataLakeGen2Item` il cmdlet per applicare il nuovo ACL.

In questo esempio viene concessa a un gruppo l'autorizzazione di scrittura ed esecuzione per una directory.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Get the directory ACL
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname).ACL

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

# To avoid duplicate ACL, remove the ACL entries that will be added later.
foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "group" -and $a.DefaultScope -eq $true-and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}

# Add ACL Entries
$aclnew = New-AzDataLakeGen2ItemAclObject -AccessControlType group -EntityId $id -Permission "-wx" -DefaultScope -InputObject $aclnew

# Update ACL on server
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname -Acl $aclnew  

```

In questo esempio viene concessa a un gruppo l'autorizzazione di scrittura ed esecuzione per un file.

```powershell
$filesystemName = "my-file-system"
$fileName = "my-directory/upload.txt"
$Id = "xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"

# Get the file ACL
$acl = (Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName).ACL

# Create the new ACL object.
[Collections.Generic.List[System.Object]]$aclnew =$acl

# To avoid duplicate ACL, remove the ACL entries that will be added later.
foreach ($a in $aclnew)
{
    if ($a.AccessControlType -eq "group" -and $a.DefaultScope -eq $true-and $a.EntityId -eq $id)
    {
        $aclnew.Remove($a);
        break;
    }
}

# Add ACL Entries
$aclnew = New-AzDataLakeGen2ItemAclObject -AccessControlType group -EntityId $id -Permission "-wx" -DefaultScope -InputObject $aclnew

# Update ACL on server
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $fileName -Acl $aclnew 

```

### <a name="set-permissions-on-all-items-in-a-file-system"></a>Impostare le autorizzazioni per tutti gli elementi in un file system

È possibile `Get-AzDataLakeGen2Item` utilizzare `-Recurse` il parametro `Update-AzDataLakeGen2Item` e con il cmdlet per impostare in modo ricorsivo l'ACL di tutte le directory e i file in un file system. 

```powershell
$filesystemName = "my-file-system"
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = New-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission "-wx" -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse -FetchPermission | Update-AzDataLakeGen2Item -Acl $acl
```
<a id="gen1-gen2-map" />

## <a name="gen1-to-gen2-mapping"></a>Mapping da Gen1 a Gen2

Nella tabella seguente viene illustrato il mapping dei cmdlet utilizzati per Data Lake Storage Gen1 ai cmdlet per Data Lake Storage Gen2.

|Cmdlet Data Lake Storage Gen1| Cmdlet Data Lake Storage Gen2| Note |
|--------|---------|-----|
|Get-AzDataLakeStoreChildItem|Get-AzDataLakeGen2ChildItem|Per impostazione predefinita, il cmdlet Get-AzDataLakeGen2ChildItem elenca solo gli elementi figlio di primo livello. Il parametro -Recurse elenca gli elementi figlio in modo ricorsivo. |
|Get-AzDataLakeStoreItem<br>Get-AzDataLakeStoreItemAclEntry<br>Get-AzDataLakeStoreItemOwner<br>Get-AzDataLakeStoreItemPermission|Get-AzDataLakeGen2Item|Gli elementi di output del cmdlet Get-AzDataLakeGen2Item hanno le seguenti proprietà: Acl, Owner, Group, Permission.|
|Get-AzDataLakeStoreItemContent|Get-AzDataLakeGen2FileContent|Il cmdlet Get-AzDataLakeGen2FileContent scarica il contenuto del file nel file locale.|
|Spostamento-AzDataLakeStoreItem|Spostamento-AzDataLakeGen2Item||
|Nuovo-AzDataLakeStoreItem|New-AzDataLakeGen2Item|Questo cmdlet carica il nuovo contenuto del file da un file locale.|
|Rimuovi-AzDataLakeStoreItem|Rimuovi-AzDataLakeGen2Item||
|Set-AzDataLakeStoreItemOwner<br>Set-AzDataLakeStoreItemPermission<br>Set-AzDataLakeStoreItemAcl|Aggiornamento-AzDataLakeGen2Item|Il cmdlet Update-AzDataLakeGen2Item aggiorna solo un singolo elemento e non in modo ricorsivo. Se si desidera eseguire l'aggiornamento in modo ricorsivo, elencare gli elementi utilizzando il cmdlet Get-AzDataLakeStoreChildItem , quindi eseguire la pipeline al cmdlet Update-AzDataLakeGen2Item.|
|Test-AzDataLakeStoreItem|Get-AzDataLakeGen2Item|Il cmdlet Get-AzDataLakeGen2Item segnalerà un errore se l'elemento non esiste.|



## <a name="see-also"></a>Vedere anche

* [Problemi noti](data-lake-storage-known-issues.md#api-scope-data-lake-client-library)
* [Uso di Azure PowerShell con Archiviazione di Azure.](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Cmdlet di PowerShell per l'archiviazione](/powershell/module/az.storage).

