---
title: Azure Data Lake Storage Gen2 PowerShell per file & ACL
description: Utilizzare i cmdlet di PowerShell per gestire le directory e gli elenchi di controllo di accesso (ACL) di file e directory negli account di archiviazione con spazio dei nomi gerarchico abilitato.
services: storage
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 04/21/2020
ms.author: normesta
ms.reviewer: prishet
ms.openlocfilehash: 68ffe40f93be3d10666ebad2eaa153fc9dc9687f
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768028"
---
# <a name="use-powershell-to-manage-directories-files-and-acls-in-azure-data-lake-storage-gen2"></a>Usare PowerShell per gestire directory, file e ACL in Azure Data Lake Storage Gen2Use PowerShell to manage directories, files, and ACLs in Azure Data Lake Storage Gen2

Questo articolo illustra come usare PowerShell per creare e gestire directory, file e autorizzazioni negli account di archiviazione con spazio dei nomi gerarchico abilitato. 

[Mapping da Gen1 a Gen2](#gen1-gen2-map) | [Inviare commenti e suggerimentiGen1](https://github.com/Azure/azure-powershell/issues) to Gen2 mapping Give feedback

## <a name="prerequisites"></a>Prerequisiti

> [!div class="checklist"]
> * Una sottoscrizione di Azure. Vedere [Ottenere una versione di prova gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Un account di archiviazione con spazio dei nomi gerarchico abilitato. Seguire [queste](data-lake-storage-quickstart-create-account.md) istruzioni per crearne una.
> * .NET Framework è 4.7.2 o versione successiva. Vedere [Scaricare .NET Framework](https://dotnet.microsoft.com/download/dotnet-framework).
> * Versione `5.1` di PowerShell o versione successiva.

## <a name="install-the-powershell-module"></a>Installa il modulo PowerShell

1. Verificare che la versione di `5.1` PowerShell installata sia o successiva utilizzando il comando seguente.    

   ```powershell
   echo $PSVersionTable.PSVersion.ToString() 
   ```
    
   Per aggiornare la versione di PowerShell, vedere [Upgrading existing Windows PowerShell](https://docs.microsoft.com/powershell/scripting/install/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell)
    
2. Installare il modulo **Az.Storage.**

   ```powershell
   Install-Module Az.Storage -Repository PSGallery -Force  
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
$dir.Group
$dir.Owner
$dir.Properties
$dir.Properties.Metadata
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

> [!NOTE]
> Utilizzare `-Force` il parametro se si desidera sovrascrivere senza richieste.

In questo esempio `my-directory` viene spostata `my-directory-2` una `my-subdirectory`directory denominata in una sottodirectory denominata . 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$dirname2 = "my-directory-2/my-subdirectory/"
Move-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Path $dirname1 -DestFileSystem $filesystemName -DestPath $dirname2
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

Elencare il contenuto di `Get-AzDataLakeGen2ChildItem` una directory utilizzando il cmdlet. È possibile utilizzare `-OutputUserPrincipalName` il parametro facoltativo per ottenere il nome (anziché l'ID oggetto) degli utenti.

In questo esempio viene elencato il contenuto di una directory denominata `my-directory`.

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -OutputUserPrincipalName
```

Nell'esempio riportato `Group`di `Owner` seguito vengono elencate le `ACL`proprietà , `Permissions`, e di ciascun elemento della directory. Il `-FetchProperty` parametro è necessario `ACL` per ottenere i valori per la proprietà. 

```powershell
$filesystemName = "my-file-system"
$dirname = "my-directory/"
$properties = Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Path $dirname -Recurse -FetchProperty
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
$file1.Properties
$file1.Properties.Metadata

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
$file.Group
$file.Owner
$file.Properties
$file.Properties.Metadata
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

È possibile ottenere, impostare e aggiornare le autorizzazioni di accesso di file system, directory e file. Queste autorizzazioni vengono acquisite negli elenchi di controllo di accesso (ACL).

> [!NOTE]
> Se si usa Azure Active Directory (Azure AD) per autorizzare i comandi, assicurarsi che all'entità di sicurezza sia stato assegnato il ruolo Proprietario dati BLOB di [archiviazione.](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#storage-blob-data-owner) Per altre informazioni sull'applicazione delle autorizzazioni ACL e sugli effetti della modifica, vedere Controllo di [accesso in Archiviazione di Azure Data Lake Gen2.](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-access-control)

### <a name="get-an-acl"></a>Ottenere un ACLGet an ACL

Ottenere l'ACL di una directory `Get-AzDataLakeGen2Item`o di un file utilizzando il cmdlet.

In questo esempio viene ottenuto l'ACL di un **file system** e quindi viene stampato l'ACL nella console.

```powershell
$filesystemName = "my-file-system"
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

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

### <a name="set-an-acl"></a>Impostare un ACL

Utilizzare `set-AzDataLakeGen2ItemAclObject` il cmdlet per creare un ACL per l'utente proprietario, il gruppo proprietario o altri utenti. Quindi, utilizzare `Update-AzDataLakeGen2Item` il cmdlet per eseguire il commit dell'ACL.

In questo esempio viene impostato l'ACL in un **file system** per l'utente proprietario, il gruppo proprietario o altri utenti, quindi l'ACL viene stampato nella console.

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Update-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName -Acl $acl
$filesystem = Get-AzDataLakeGen2Item -Context $ctx -FileSystem $filesystemName
$filesystem.ACL
```

In questo esempio l'ACL viene impostato su una **directory** per l'utente proprietario, il gruppo proprietario o altri utenti, quindi l'ACL viene stampato nella console.

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
In questo esempio viene impostato l'ACL su un **file** per l'utente proprietario, il gruppo proprietario o altri utenti e quindi viene stampato l'ACL nella console.

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

L'immagine seguente mostra l'output dopo aver impostato l'ACL di un file.

![Ottenere l'output ACLGet ACL output](./media/data-lake-storage-directory-file-acl-powershell/set-acl.png)

In questo esempio, l'utente proprietario e il gruppo proprietario dispongono solo delle autorizzazioni di lettura e scrittura. Tutti gli altri utenti dispongono di autorizzazioni di scrittura ed esecuzione. Per altre informazioni sugli elenchi di controllo di accesso, vedere [Controllo di accesso in Azure Data Lake Storage Gen2.For](data-lake-storage-access-control.md)more information about access control lists, see Access control in Azure Data Lake Storage Gen2 .


### <a name="set-acls-on-all-items-in-a-file-system"></a>Impostare gli ACL su tutti gli elementi in un file system

È possibile `Get-AzDataLakeGen2Item` utilizzare `-Recurse` il parametro `Update-AzDataLakeGen2Item` e con il cmdlet per impostare in modo ricorsivo l'ACL di tutte le directory e i file in un file system. 

```powershell
$filesystemName = "my-file-system"
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType user -Permission rw- 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType group -Permission rw- -InputObject $acl 
$acl = set-AzDataLakeGen2ItemAclObject -AccessControlType other -Permission -wx -InputObject $acl
Get-AzDataLakeGen2ChildItem -Context $ctx -FileSystem $filesystemName -Recurse | Update-AzDataLakeGen2Item -Acl $acl
```
### <a name="add-or-update-an-acl-entry"></a>Aggiungere o aggiornare una voce ACL

In primo luogo, ottenere l'ACL. Quindi, utilizzare `set-AzDataLakeGen2ItemAclObject` il cmdlet per aggiungere o aggiornare una voce ACL. Utilizzare `Update-AzDataLakeGen2Item` il cmdlet per eseguire il commit dell'ACL.

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

