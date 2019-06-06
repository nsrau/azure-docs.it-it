---
title: Guida introduttiva per la gestione di condivisioni file di Azure con Azure PowerShell
description: Questa guida introduttiva illustra come gestire condivisioni file di Azure con Azure PowerShell.
services: storage
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 10/26/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e32aead791fb84415da1b00f1e979a6ac0f28155
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2019
ms.locfileid: "66729030"
---
# <a name="quickstart-create-and-manage-an-azure-file-share-with-azure-powershell"></a>Avvio rapido: Creare e gestire una condivisione file di Azure con Azure PowerShell 
Questa guida contiene tutte le informazioni essenziali sull'uso delle [condivisioni file di Azure](storage-files-introduction.md) con PowerShell. Le condivisioni file di Azure sono esattamente come le altre condivisioni file, ma vengono archiviate nel cloud e sono supportate dalla piattaforma Azure. Le condivisioni file di Azure supportano il protocollo SMB standard di settore e consentono la condivisione di file in più computer, applicazioni e istanze. 

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Se si vuole installare e usare PowerShell in locale, questa guida richiede il modulo Az di Azure PowerShell versione 0.7 o successive. Per determinare la versione del modulo Azure PowerShell in esecuzione, eseguire `Get-Module -ListAvailable Az`. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-Az-ps). Se si esegue PowerShell in locale, è necessario anche eseguire `Login-AzAccount` per accedere all'account Azure.

## <a name="create-a-resource-group"></a>Creare un gruppo di risorse
Un gruppo di risorse è un contenitore logico in cui le risorse di Azure vengono distribuite e gestite. Se non è già disponibile un gruppo di risorse di Azure, è possibile crearne uno nuovo con il cmdlet [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). 

L'esempio seguente crea un gruppo di risorse denominato *myResourceGroup* nell'area Stati Uniti orientali:

```azurepowershell-interactive
New-AzResourceGroup `
    -Name myResourceGroup `
    -Location EastUS
```

## <a name="create-a-storage-account"></a>Creare un account di archiviazione
Un account di archiviazione è un pool condiviso di spazio di archiviazione in cui è possibile distribuire condivisioni file di Azure o altre risorse di archiviazione, ad esempio BLOB o code. Un account di archiviazione può contenere un numero illimitato di condivisioni e una condivisione può archiviare un numero illimitato di file, fino ai limiti di capacità dell'account di archiviazione.

Questo esempio crea un account di archiviazione con il cmdlet [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount). L'account di archiviazione viene denominato *mystorageaccount\<numero casuale>* e nella variabile **$storageAcct** viene inserito un riferimento a tale account di archiviazione. I nomi degli account di archiviazione devono essere univoci, quindi usare `Get-Random` per aggiungere un numero al nome e renderlo univoco. 

```azurepowershell-interactive 
$storageAcct = New-AzStorageAccount `
                  -ResourceGroupName "myResourceGroup" `
                  -Name "mystorageacct$(Get-Random)" `
                  -Location eastus `
                  -SkuName Standard_LRS 
```

## <a name="create-an-azure-file-share"></a>Creare una condivisione file di Azure
È ora possibile creare la prima condivisione file di Azure. È possibile creare una condivisione file usando il cmdlet [New-AzStorageShare](/powershell/module/az.storage/New-AzStorageShare). In questo esempio viene creata una condivisione denominata `myshare`.

```azurepowershell-interactive
New-AzStorageShare `
   -Name myshare `
   -Context $storageAcct.Context
```

I nomi delle condivisioni devono essere costituiti da lettere minuscole, numeri e trattini singoli, ma non possono iniziare con un trattino. Per dettagli su come denominare condivisioni e file, vedere [Denominazione e riferimento a condivisioni, directory, file e metadati](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

## <a name="use-your-azure-file-share"></a>Usare la condivisione file di Azure
File di Azure offre due metodi per usare i file e le cartelle all'interno della condivisione file di Azure: il [protocollo SMB (Server Message Block)](https://msdn.microsoft.com/library/windows/desktop/aa365233.aspx) standard di settore e il [protocollo REST di File](https://docs.microsoft.com/rest/api/storageservices/file-service-rest-api). 

Per montare una condivisione file con SMB, vedere il documento riportato di seguito in base al sistema operativo in uso:
- [Windows](storage-how-to-use-files-windows.md)
- [Linux](storage-how-to-use-files-linux.md)
- [macOS](storage-how-to-use-files-mac.md)

### <a name="using-an-azure-file-share-with-the-file-rest-protocol"></a>Uso di una condivisione file di Azure con il protocollo REST di File 
È possibile usare il protocollo REST File direttamente, ovvero creando personalmente chiamate HTTP REST, ma il metodo più comune consiste nell'usare il modulo di Azure PowerShell, l'[interfaccia della riga di comando di Azure](storage-how-to-use-files-cli.md) o Azure Storage SDK, perché offrono un pratico wrapper per il protocollo REST File nel linguaggio di scripting/programmazione preferito.  

Nella maggior parte dei casi si userà la condivisione file di Azure tramite il protocollo SMB, perché questo consente di usare le applicazioni e gli strumenti esistenti con cui si può avere familiarità, ma esistono diversi motivi per cui è vantaggioso usare l'API REST di File invece di SMB, ad esempio:

- Si esplora la condivisione file da Cloud Shell di PowerShell, che non può montare condivisioni file tramite SMB.
- È necessario eseguire uno script o un'applicazione da client in cui non può essere montata una condivisione SMB, ad esempio client locali in cui la porta 445 non è sbloccata.
- Si sfruttano risorse serverless, ad esempio [Funzioni di Azure](../../azure-functions/functions-overview.md). 

Gli esempi seguenti mostrano come usare il modulo di Azure PowerShell per modificare la condivisione file di Azure con il protocollo REST File. 

#### <a name="create-directory"></a>Creare la directory
Per creare una nuova directory denominata *myDirectory* nella radice della condivisione file di Azure, usare il cmdlet [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory).

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Path "myDirectory"
```

#### <a name="upload-a-file"></a>Caricare un file
Per una dimostrazione del caricamento di un file con il cmdlet [Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent), è prima di tutto necessario creare un file da caricare nell'unità dei file temporanei di Cloud Shell di PowerShell. 

Questo esempio inserisce la data e l'ora correnti in un nuovo file nell'unità dei file temporanei, quindi carica il file nella condivisione file.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
Get-Date | Out-File -FilePath "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName "myshare" `
   -Source "C:\Users\ContainerAdministrator\CloudDrive\SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

Se si esegue PowerShell in locale, sostituire `C:\Users\ContainerAdministrator\CloudDrive\` con un percorso esistente nel computer in uso.

Dopo il caricamento del file, è possibile usare il cmdlet [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) per verificare che sia stato caricato nella condivisione file di Azure. 

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare" -Path "myDirectory" 
```

#### <a name="download-a-file"></a>Scaricare un file
È possibile usare il cmdlet [Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) per scaricare una copia del file appena caricato nell'unità dei file temporanei di Cloud Shell.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
     -Path "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt" `
     -Force `
     -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt" ` 
    -Destination "C:\Users\ContainerAdministrator\CloudDrive\SampleDownload.txt"
```

Dopo aver scaricato il file, è possibile usare `Get-ChildItem` per verificare che il file sia stato scaricato nell'unità dei file temporanei di Cloud Shell di PowerShell.

```azurepowershell-interactive
Get-ChildItem -Path "C:\Users\ContainerAdministrator\CloudDrive"
``` 

#### <a name="copy-files"></a>Copiare i file
Un'attività comune è la copia dei file da una condivisione file a un'altra oppure a/da un contenitore di archiviazione BLOB di Azure. Per una dimostrazione di questa funzionalità, è possibile creare una nuova condivisione e copiare il file appena caricato in questa nuova condivisione con il cmdlet [Start-AzStorageFileCopy](/powershell/module/az.storage/Start-AzStorageFileCopy). 

```azurepowershell-interactive
New-AzStorageShare `
    -Name "myshare2" `
    -Context $storageAcct.Context
  
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName "myshare2" `
   -Path "myDirectory2"

Start-AzStorageFileCopy `
    -Context $storageAcct.Context `
    -SrcShareName "myshare" `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestShareName "myshare2" `
    -DestFilePath "myDirectory2\SampleCopy.txt" `
    -DestContext $storageAcct.Context
```

Se ora si elencano i file nella nuova condivisione, verrà visualizzato il file copiato.

```azurepowershell-interactive
Get-AzStorageFile -Context $storageAcct.Context -ShareName "myshare2" -Path "myDirectory2" 
```

Anche se il cmdlet `Start-AzStorageFileCopy` è utile per gli spostamenti di file ad hoc tra condivisioni file di Azure e contenitori di archiviazione BLOB di Azure, è consigliabile usare AzCopy per gli spostamenti più grandi (in termini di numero o di dimensioni dei file da spostare). Sono disponibili altre informazioni su [AzCopy per Windows](../common/storage-use-azcopy.md) e [AzCopy per Linux](../common/storage-use-azcopy-linux.md). AzCopy deve essere installato in locale, non è disponibile in Cloud Shell. 

## <a name="create-and-manage-share-snapshots"></a>Creare e gestire gli snapshot di condivisione
Un'altra utile attività da eseguire con una condivisione file di Azure è la creazione degli snapshot di condivisione. Uno snapshot mantiene una copia di una condivisione file di Azure eseguita in un momento specifico. Gli snapshot di condivisione sono simili alle tecnologie del sistema operativo con cui probabilmente si ha già familiarità, ad esempio:
- [Servizio Copia Shadow del volume](https://docs.microsoft.com/windows/desktop/VSS/volume-shadow-copy-service-portal) per i file system Windows come NTFS e ReFS
- Snapshot [Logical Volume Manager (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) per i sistemi Linux
- Snapshot [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) per macOS. 
 È possibile creare uno snapshot di condivisione usando il metodo `Snapshot` sull'oggetto di PowerShell per una condivisione file, recuperato con il cmdlet [Get-AzStorageShare](/powershell/module/az.storage/Get-AzStorageShare). 

```azurepowershell-interactive
$share = Get-AzStorageShare -Context $storageAcct.Context -Name "myshare"
$snapshot = $share.Snapshot()
```

### <a name="browse-share-snapshots"></a>Esplorare gli snapshot di condivisione
È possibile esplorare il contenuto dello snapshot di condivisione passando il riferimento allo snapshot (`$snapshot`) al parametro `-Share` del cmdlet `Get-AzStorageFile`.

```azurepowershell-interactive
Get-AzStorageFile -Share $snapshot
```

### <a name="list-share-snapshots"></a>Elencare gli snapshot di condivisione
È possibile visualizzare l'elenco di snapshot acquisiti per la condivisione con il comando seguente.

```azurepowershell-interactive
Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.Name -eq "myshare" -and $_.IsSnapshot -eq $true }
```

### <a name="restore-from-a-share-snapshot"></a>Eseguire il ripristino da snapshot di condivisione
È possibile ripristinare un file con il comando `Start-AzStorageFileCopy` usato prima. Ai fini di questa guida introduttiva, si eliminerà prima il file `SampleUpload.txt` caricato in precedenza, così da poterlo ripristinare dallo snapshot.

```azurepowershell-interactive
# Delete SampleUpload.txt
Remove-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName "myshare" `
    -Path "myDirectory\SampleUpload.txt"
 # Restore SampleUpload.txt from the share snapshot
Start-AzStorageFileCopy `
    -SrcShare $snapshot `
    -SrcFilePath "myDirectory\SampleUpload.txt" `
    -DestContext $storageAcct.Context `
    -DestShareName "myshare" `
    -DestFilePath "myDirectory\SampleUpload.txt"
```

### <a name="delete-a-share-snapshot"></a>Eliminare uno snapshot di condivisione
È possibile eliminare uno snapshot di condivisione usando il cmdlet [Remove-AzStorageShare](/powershell/module/az.storage/Remove-AzStorageShare) con la variabile contenente il riferimento `$snapshot` al parametro `-Share`.

```azurepowershell-interactive
Remove-AzStorageShare -Share $snapshot
```

## <a name="clean-up-resources"></a>Pulire le risorse
Al termine, è possibile usare il cmdlet [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) per rimuovere il gruppo di risorse e tutte le risorse correlate. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

È anche possibile rimuovere le risorse singolarmente:

- Per rimuovere le condivisioni file di Azure create per questa guida introduttiva.

    ```azurepowershell-interactive
    Get-AzStorageShare -Context $storageAcct.Context | Where-Object { $_.IsSnapshot -eq $false } | ForEach-Object { 
        Remove-AzStorageShare -Context $storageAcct.Context -Name $_.Name
    }
    ```

- Per rimuovere l'account di archiviazione stesso. In questo modo verranno implicitamente rimosse le condivisioni file di Azure create, oltre a eventuali altre risorse di archiviazione create, ad esempio un contenitore di archiviazione BLOB di Azure.

    ```azurepowershell-interactive
    Remove-AzStorageAccount -ResourceGroupName $storageAcct.ResourceGroupName -Name $storageAcct.StorageAccountName
    ```

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Che cos'è File di Azure?](storage-files-introduction.md)
