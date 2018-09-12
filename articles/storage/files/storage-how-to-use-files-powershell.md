---
title: Gestione di condivisioni file di Azure con Azure PowerShell
description: Informazioni sulla gestione di condivisioni file di Azure con Azure PowerShell
services: storage
author: wmgries
ms.service: storage
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.component: files
ms.openlocfilehash: 72f320279d23dc701f045ee6db1681a355f98808
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2018
ms.locfileid: "44022287"
---
# <a name="managing-azure-file-shares-with-azure-powershell"></a>Gestione di condivisioni file di Azure con Azure PowerShell 
[File di Azure](storage-files-introduction.md) è il file system cloud facile da usare di Microsoft. Le condivisioni file di Azure possono essere montate in Windows, Linux e macOS. Questa guida contiene tutte le informazioni essenziali sull'uso delle condivisioni file di Azure con PowerShell. In questo articolo viene spiegato come:

> [!div class="checklist"]
> * Creare un gruppo di risorse e un account di archiviazione
> * Creare una condivisione file di Azure 
> * Creare una directory
> * Caricare un file 
> * Scaricare un file
> * Creare e usare uno snapshot di condivisione

Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [cloud-shell-powershell.md](../../../includes/cloud-shell-powershell.md)]

Se si preferisce installare e usare PowerShell in locale, questa guida richiede il modulo Azure PowerShell versione 5.1.1 o successiva. Per determinare la versione del modulo Azure PowerShell in esecuzione, eseguire `Get-Module -ListAvailable AzureRM`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps). Se si esegue PowerShell in locale, è anche necessario eseguire `Connect-AzureRmAccount` per creare una connessione con Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Se non è già disponibile un gruppo di risorse di Azure, è possibile crearne uno nuovo con il cmdlet [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area Stati Uniti orientali:

```azurepowershell-interactive
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Un account di archiviazione è un pool condiviso di spazio di archiviazione in cui è possibile distribuire condivisioni file di Azure o altre risorse di archiviazione, ad esempio BLOB o code. Un account di archiviazione può contenere un numero illimitato di condivisioni e una condivisione può archiviare un numero illimitato di file, fino ai limiti di capacità dell'account di archiviazione.

In questo esempio viene creato un account di archiviazione denominato `mystorageacct<random number>` e viene inserito un riferimento a tale account di archiviazione nella variabile **$storageAcct**. I nomi degli account di archiviazione devono essere univoci, quindi usare `Get-Random` per aggiungere un numero al nome e renderlo univoco. 

```azurepowershell-interactive 
$storageAcct = New-AzureRmStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure
È ora possibile creare la prima condivisione file di Azure. È possibile creare una condivisione file usando il cmdlet [New-AzureStorageShare](https://docs.microsoft.com/powershell/module/servicemanagement/azure.storage/new-azurestorageshare). In questo esempio viene creata una condivisione denominata `myshare`.

```azurepowershell-interactive
New-AzureStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

> [!Important]  
> I nomi condivisione devono essere costituiti da lettere minuscole, numeri e trattini singoli, ma non possono iniziare con un trattino. Per dettagli su come denominare condivisioni e file, vedere [Denominazione e riferimento a condivisioni, directory, file e metadati](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="work-with-the-contents-of-the-azure-file-share"></a>Usare il contenuto della condivisione file di Azure
Dopo avere creato una condivisione file di Azure, è possibile montare la condivisione file con SMB in [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) o [macOS](storage-how-to-use-files-mac.md). In alternativa, è possibile usare la condivisione file di Azure con il modulo Azure PowerShell. Questo è un vantaggio rispetto al montaggio della condivisione file con SMB, perché tutte le richieste eseguite con PowerShell vengono effettuate con l'API REST di File, che consente di creare, modificare ed eliminare file e directory nella condivisione file da:

- Cloud Shell di PowerShell (che non può montare le condivisioni file tramite SMB).
- Client che non possono montare le condivisioni SMB, ad esempio client locali con la porta 445 bloccata.
- Scenari senza server, ad esempio in [Funzioni di Azure](../../azure-functions/functions-overview.md). 


### <a name="create-directory"></a>Creare la directory
Per creare una nuova directory denominata *myDirectory* nella radice della condivisione file di Azure, usare il cmdlet [New-AzureStorageDirectory](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragedirectory).


```azurepowershell-interactive
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

### <a name="upload-a-file"></a>Caricare un file
Per una dimostrazione del caricamento di un file con il cmdlet [Set-AzureStorageFileContent](/powershell/module/azure.storage/set-azurestoragefilecontent), è prima di tutto necessario creare un file da caricare nell'unità dei file temporanei di Cloud Shell di PowerShell. 

Questo esempio inserisce la data e l'ora correnti in un nuovo file nell'unità dei file temporanei, quindi carica il file nella condivisione file.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzureStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Se si esegue PowerShell in locale, sostituire `C:\Users\ContainerAdministrator\CloudDrive\` con un percorso esistente nel computer in uso.

Dopo il caricamento del file, è possibile usare il cmdlet [Get-AzureStorageFile](/powershell/module/Azure.Storage/Get-AzureStorageFile) per verificare che sia stato caricato nella condivisione file di Azure. 

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

### <a name="download-a-file"></a>Scaricare un file
È possibile usare il cmdlet [Get-AzureStorageFileContent](/powershell/module/azure.storage/get-azurestoragefilecontent) per scaricare una copia del file appena caricato nell'unità dei file temporanei di Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item 
    `-Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzureStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

Dopo aver scaricato il file, è possibile usare `Get-ChildItem` per verificare che il file sia stato scaricato nell'unità dei file temporanei di Cloud Shell di PowerShell.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

### <a name="copy-files"></a>Copiare i file
Un'attività comune è la copia dei file da una condivisione file a un'altra oppure a/da un contenitore di archiviazione BLOB di Azure. Per una dimostrazione di questa funzionalità, è possibile creare una nuova condivisione e copiare il file appena caricato in questa nuova condivisione con il cmdlet [Start-AzureStorageFileCopy](/powershell/module/azure.storage/start-azurestoragefilecopy). 

```azurepowershell-interactive
New-AzureStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzureStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzureStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Se ora si elencano i file nella nuova condivisione, verrà visualizzato il file copiato.

```azurepowershell-interactive
Get-AzureStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Anche se il cmdlet `Start-AzureStorageFileCopy` è utile per gli spostamenti di file ad hoc tra condivisioni file di Azure e contenitori di archiviazione BLOB di Azure, è consigliabile usare AzCopy per gli spostamenti più grandi (in termini di numero o di dimensioni dei file da spostare). Sono disponibili altre informazioni su [AzCopy per Windows](../common/storage-use-azcopy.md) e [AzCopy per Linux](../common/storage-use-azcopy-linux.md). AzCopy deve essere installato in locale, non è disponibile in Cloud Shell. 

## <a name="create-and-modify-share-snapshots"></a>Creare e modificare gli snapshot di condivisione
Un'altra utile attività da eseguire con una condivisione file di Azure è la creazione degli snapshot di condivisione. Uno snapshot mantiene una copia di una condivisione file di Azure eseguita in un momento specifico. Gli snapshot di condivisione sono simili alle tecnologie del sistema operativo con cui probabilmente si ha già familiarità, ad esempio:
- [Servizio Copia Shadow del volume](https://docs.microsoft.com/en-us/windows/desktop/VSS/volume-shadow-copy-service-portal) per i file system Windows come NTFS e ReFS
- Snapshot [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) per i sistemi Linux
- Snapshot [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) per macOS. 

È possibile creare uno snapshot di condivisione usando il metodo `Snapshot` sull'oggetto di PowerShell per una condivisione file, recuperato con il cmdlet [Get-AzureStorageShare](/powershell/module/azure.storage/get-azurestorageshare). 

```azurepowershell-interactive
$share = Get-AzureStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Esplorare gli snapshot di condivisione
È possibile esplorare il contenuto dello snapshot di condivisione passando il riferimento allo snapshot (`$snapshot`) al parametro `-Share` del cmdlet `Get-AzureStorageFile`.

```azurepowershell-interactive
Get-AzureStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Elencare gli snapshot di condivisione
È possibile visualizzare l'elenco di snapshot acquisiti per la condivisione con il comando seguente.

```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Eseguire il ripristino da snapshot di condivisione
È possibile ripristinare un file con il comando `Start-AzureStorageFileCopy` usato prima. Ai fini di questa guida introduttiva, si eliminerà prima il file `SampleUpload.txt` caricato in precedenza, così da poterlo ripristinare dallo snapshot.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzureStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"

# Restore SampleUpload.txt from the share snapshot
Start-AzureStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Eliminare uno snapshot di condivisione
È possibile eliminare uno snapshot di condivisione usando il cmdlet [Remove-AzureStorageShare](/powershell/module/azure.storage/remove-azurestorageshare) con la variabile contenente il riferimento `$snapshot` al parametro `-Share`.

```azurepowershell-interactive
Remove-AzureStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Pulire le risorse
Al termine è possibile usare il cmdlet [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse correlate. 

```azurepowershell-interactive
Remove-AzureRmResourceGroup -Name myResourceGroup
```

È anche possibile rimuovere le risorse singolarmente:

- Per rimuovere le condivisioni file di Azure create per questa guida introduttiva.
```azurepowershell-interactive
Get-AzureStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
    Remove-AzureStorageShare -Context $storageAcct.Context -Name $_.Name
}
```

- Per rimuovere l'account di archiviazione stesso. In questo modo verranno implicitamente rimosse le condivisioni file di Azure create, oltre a eventuali altre risorse di archiviazione create, ad esempio un contenitore di archiviazione BLOB di Azure.
```azurepowershell-interactive
Remove-AzureRmStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
```

## <a name="next-steps"></a>Passaggi successivi
- [Gestione di condivisioni file con il portale di Azure](storage-how-to-use-files-portal.md)
- [Gestione di condivisioni file con l'interfaccia della riga di comando di Azure](storage-how-to-use-files-cli.md)
- [Gestione di condivisioni file con Storage Explorer](storage-how-to-use-files-storage-explorer.md)
- [Pianificazione per la distribuzione di File di Azure](storage-files-planning.md)
