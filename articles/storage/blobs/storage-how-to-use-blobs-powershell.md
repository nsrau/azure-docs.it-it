---
title: Eseguire operazioni in Archiviazione BLOB di Azure (oggetto archiviazione) con PowerShell | Microsoft Docs
description: Esercitazione - Eseguire operazioni in Archiviazione BLOB di Azure (oggetto archiviazione) con PowerShell
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 09/14/2017
ms.author: tamram
ms.openlocfilehash: 8b87ecc3fefe5338269a6ad584e9a097ff3fb633
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/24/2018
---
# <a name="perform-azure-blob-storage-operations-with-azure-powershell"></a>Eseguire operazioni in Archiviazione Blob di Azure con Azure PowerShell

Archivio BLOB di Azure è un servizio per l'archiviazione di grandi quantità di dati oggetto non strutturati, ad esempio dati di testo o binari, a cui è possibile accedere da qualsiasi parte del mondo tramite HTTP o HTTPS. L'articolo illustra le operazioni di base in Archiviazione Blob di Azure, ad esempio il caricamento, il download e l'eliminazione dei BLOB. Si apprenderà come:

> [!div class="checklist"]
> * Creare un contenitore 
> * Caricare i BLOB
> * Elencare i BLOB in un contenitore 
> * Scaricare BLOB
> * Copiare i BLOB
> * Eliminare BLOB
> * Visualizzare e impostare le proprietà e i metadati di un BLOB
> * Gestire la protezione tramite le firme di accesso condiviso

Questa esercitazione richiede il modulo Azure PowerShell 3.6 o versioni successive. Eseguire `Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

[!INCLUDE [storage-quickstart-tutorial-intro-include-powershell](../../../includes/storage-quickstart-tutorial-intro-include-powershell.md)]

## <a name="create-a-container"></a>Creare un contenitore

Gli elementi BLOB vengono sempre caricati in un contenitore. I contenitori sono simili alle directory nel computer e di fatto consentono di organizzare i gruppi di BLOB così come si organizzano i file nelle cartelle del computer. Un account di archiviazione può disporre di un numero illimitato di contenitori; l'unico limite è la quantità di spazio occupato nell'account di archiviazione (fino a 500 TB). 

Per definire chi può accedere ai BLOB in un contenitore, al momento della creazione è possibile impostare il livello di accesso. L'impostazione Privato (livello di accesso = `Off`) fa sì che nessuno possa accedervi senza una firma di accesso condiviso o le chiavi di accesso dell'account di archiviazione. Se il livello di accesso non viene specificato al momento della creazione del contenitore, viene automaticamente impostato su privato.

Può essere necessario rendere pubblicamente accessibili le immagini nel contenitore. Ad esempio, se si vogliono archiviare immagini da visualizzare nel sito Web, queste dovranno essere pubbliche. In questo caso, il livello di accesso al contenitore va impostato su `blob`: chiunque con un URL che punta a un BLOB nel contenitore può accedere al BLOB.

Per creare il contenitore, impostare il nome del contenitore, quindi creare il contenitore e impostare le autorizzazioni su 'BLOB'. I nomi dei contenitori devono iniziare con una lettera o un numero e possono contenere solo lettere, numeri e il carattere trattino (-). Per altre informazioni sulla denominazione dei BLOB e dei contenitori, vedere l'articolo relativo a [denominazione e riferimento a contenitori, BLOB e metadati](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

Creare un nuovo contenitore con [New-AzureStorageContainer](/powershell/module/azure.storage/new-azurestoragecontainer). Impostare il livello di accesso su Pubblico. Il nome del contenitore in questo esempio è *howtoblobs*.

```powershell
$containerName = "howtoblobs"
New-AzureStorageContainer -Name $containerName -Context $ctx -Permission blob
```

## <a name="upload-blobs-into-a-container"></a>Caricare BLOB in un contenitore

Archiviazione BLOB di Azure supporta BLOB in blocchi, BLOB di accodamento e BLOB di pagine.  I file VHD usati per il backup di macchine virtuali IaaS sono BLOB di pagine. I BLOB di accodamento sono usati per la registrazione, ad esempio quando si vuole scrivere in un file e poi continuare ad aggiungere altre informazioni. La maggior parte dei file presenti nell'archiviazione BLOB è costituita da BLOB in blocchi. 

Per caricare un file in un BLOB in blocchi, ottenere un riferimento a un contenitore e quindi un riferimento al BLOB in blocchi nel contenitore. Dopo aver creato il riferimento al BLOB, è possibile caricarvi i dati con [Set-AzureStorageBlobContent](/powershell/module/azure.storage/set-azurestorageblobcontent). Questa operazione consentirà di creare il BLOB se non esiste o di sovrascriverlo se esiste.

Di seguito è mostrato come caricare un BLOB in un contenitore. Innanzitutto impostare le variabili che puntano alla directory nel computer locale in cui si trovano i file e impostare una variabile per il nome del file da caricare. Ciò è utile quando si vuole eseguire più volte la stessa operazione. Caricare alcuni file in modo da visualizzare più voci quando si elencano i BLOB nel contenitore.

Gli esempi seguenti caricano i file Image001.jpg e Image002.png da D:\\_TestImages sul disco locale del contenitore appena creato.

```powershell
$localFileDirectory = "D:\_TestImages\"

$blobName = "Image001.jpg"
$localFile = $localFileDirectory + $blobName
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx 
```

Caricare un altro file. 

```powershell
$blobName = "Image002.png"
$localFile = $localFileDirectory + $blobName 
Set-AzureStorageBlobContent -File $localFile `
  -Container $containerName `
  -Blob $blobName `
  -Context $ctx
```

Caricare tutti i file desiderati prima di continuare.

## <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

Per ottenere l'elenco di BLOB nel contenitore usare [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) e selezionare il nome del BLOB da visualizzare.

```powershell
Get-AzureStorageBlob -Container $ContainerName -Context $ctx | select Name 
```

## <a name="download-blobs"></a>Scaricare BLOB

Scaricare i BLOB sul disco locale. Innanzitutto, impostare una variabile che punta alla cartella locale in cui scaricare i BLOB. Quindi, per ogni BLOB da scaricare, impostare il nome e usare [Get-AzureStorageBlobContent](/powershell/module/azure.storage/get-azurestorageblobcontent).

Questo esempio copia i BLOB in D:\\_TestImages\Downloads sul disco locale. 

```powershell
# local directory to which to download the files
# example "D:\_TestImages\Downloads\"
$localTargetDirectory = "D:\_TestImages\Downloads\"

# download the first blob
$blobName = "Image001.jpg"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 

# download another blob
$blobName = "Image002.png"
Get-AzureStorageBlobContent -Blob $blobName `
  -Container $containerName `
  -Destination $localTargetDirectory `
  -Context $ctx 
```

## <a name="copy-blobs"></a>Copiare i BLOB

Nell'eseguire la copia dei BLOB devono essere considerati diversi aspetti. È possibile copiare il BLOB in un nuovo nome nello stesso percorso. È possibile copiare il BLOB in un account di archiviazione distinto. È possibile infine copiare un BLOB di grandi dimensioni (ad esempio un file VHD) in un account di archiviazione diverso. Ognuna di queste operazioni va eseguita in modo diverso.

### <a name="simple-blob-copy"></a>Copia semplice di un BLOB

È possibile creare una copia di uno dei BLOB in un contenitore copiandolo in un nuovo BLOB. In questo esempio il BLOB viene copiato nello stesso contenitore con un nome diverso, ma è facile anche creare un altro contenitore e copiarlo. Questo esempio illustra come usare [Start-AzureStorageBlobCopy](/powershell/module/azure.storage/start-azurestorageblobcopy) per copiare il BLOB. È necessario specificare l'origine e il nome del BLOB di destinazione, nonché il nome del contenitore.

```powershell
$blobName = "Image002.png"
$newBlobName = "CopyOf_" + $blobName 

Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName `
  -DestBlob $newBlobName `
  -Context $ctx 

# get list of blobs to verify the new one has been created
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

### <a name="copy-a-blob-to-a-different-storage-account"></a>Copia di un BLOB in un account di archiviazione differente 

Può essere necessario copiare un BLOB in un account di archiviazione distinto, ad esempio nel caso in cui occorra copiare un file VHD che esegue il backup di una delle macchine virtuali in un account di archiviazione differente, per eseguirne il backup. 

Configurare un secondo account di archiviazione, recuperare il contesto, configurare un contenitore nell'account di archiviazione ed eseguire la copia. Questa parte dello script è quasi identica allo script precedente, ad eccezione dell'uso del secondo account di archiviazione invece del primo.

```powershell
#create new storage account, get context 
$storageAccount2Name = "blobstutorialtestcopy"
$storageAccount2 = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccount2Name `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage `
  -EnableEncryptionService Blob

$ctx2 = $storageAccount2.Context

#create a container in the second storage account 
$containerName2 = "tutorialblobscopied"
New-AzureStorageContainer -Name $containerName2 `
  -Context $ctx2 `
  -Permission blob

# copy one of the blobs from the first storage account to the second one 
# specify the source and destination container, blob name, and context
Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -SrcContext $ctx `
  -DestContext $ctx2 

# list the blobs in the container in the second storage account
# to verify that the newly copied blob is there
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

### <a name="copying-very-large-blobs-asynchronously"></a>Copia di BLOB di grandi dimensioni in modo asincrono

Per copiare un BLOB di grandi dimensioni con più GB, è possibile avviare la copia asincrona e quindi verificarne costantemente lo stato fino al termine dell'operazione. In questo modo, non si è vincolati mentre viene eseguita l'operazione di copia.

Se la copia avviene in un account di archiviazione, è molto veloce. Se la copia avviene tra due account di archiviazione nella stessa area, l'operazione è abbastanza veloce. Se invece l'origine e la destinazione si trovano in aree distinte, l'operazione potrebbe richiedere alcune ore, a seconda della distanza e delle dimensioni del file. 

Questo script usa le stesse variabili definite nell'ultimo esempio. Acquisisce tuttavia lo stato della copia e la sottopone a query ripetutamente ogni 10 secondi fino al termine dell'operazione. Può essere necessario caricare un BLOB di grandi dimensioni nell'account di archiviazione per comprendere che il completamento può richiedere più di un'iterazione.

Usare il comando [Get-AzureStorageBlobCopyState](/powershell/module/azure.storage/get-azurestorageblobcopystate) per verificare lo stato della copia. 

```powershell
# start the blob copy, and assign the result to $blobResult
$blobResult = Start-AzureStorageBlobCopy -SrcBlob $blobName `
  -SrcContainer $containerName `
  -DestContainer $containerName2 `
  -DestBlob $blobName `
  -Context $ctx `
  -DestContext $ctx2

# get the status of the blob copy
$status = $blobResult | Get-AzureStorageBlobCopyState 
# show the value of the status
$status 

# loop until it finishes copying, pausing for 10 seconds after each iteration
# it is finished when the status is no longer 'Pending'
while ($status.Status -eq "Pending") {
    $status = $blobResult | Get-AzureStorageBlobCopyState 
    $status
    Start-Sleep 10
}

# get the list of blobs to see the new file in the second storage account 
Get-AzureStorageBlob -Container $containerName2 -Context $ctx2 | select Name 
```

## <a name="delete-blobs"></a>Eliminare BLOB

Per rimuovere BLOB da un account di archiviazione usare [Remove-AzureStorageBlob](/powershell/module/azure.storage/Remove-AzureStorageBlob). 

```powershell
$blobName = "Image001.jpg"
Remove-AzureStorageBlob -Blob $blobName -Container $containerName -Context $ctx

# get list of blobs to see the one you deleted is gone
Get-AzureStorageBlob -Container $containerName -Context $ctx | select Name 
```

## <a name="read-and-write-a-blobs-properties-and-metadata"></a>Leggere e scrivere proprietà e metadati di un BLOB

Per accedere all'insieme di proprietà e metodi per un elemento **IListBlobItem** restituito, è necessario eseguirne il cast (o convertirlo) in un oggetto **CloudBlockBlob**, **CloudPageBlob** o **CloudBlobDirectory**. Se il tipo è sconosciuto, è possibile usare un controllo del tipo per determinare quello in cui viene eseguito il cast. Il codice seguente illustra come recuperare e restituire le proprietà di uno dei BLOB caricati in precedenza con [Get-AzureStorageBlob](/powershell/module/azure.storage/get-azurestorageblob) e convertire il risultato in un oggetto CloudBlockBlob.

```powershell
# blob properties
# get a reference to the blob you uploaded -- this is an **IListBlobItem** -- then
# convert it to a CloudBlockBlob, giving you access to the properties 
# and methods of the blob 
$blobName = "Image001.jpg"
$blob = Get-AzureStorageBlob -Context $ctx `
   -Container $containerName `
   -Blob $blobName 

#convert $blob to a CloudBlockBlob object
$cloudBlockBlob = [Microsoft.WindowsAzure.Storage.Blob.CloudBlockBlob] $blob.ICloudBlob

# you can view the properties by typing in $cloudBlockBlob 
#   and hitting the period key; this brings up IntelliSense,
#   which shows you all of the available properties
Write-Host "blob type = " $cloudBlockBlob.BlobType
Write-Host "blob name = " $cloudBlockBlob.Name
Write-Host "blob uri = " $cloudBLockBlob.Uri
```

Popolare le proprietà di sistema chiamando FetchAttributes, quindi visualizzare le proprietà. Alcune proprietà sono scrivibili ed è possibile modificarne i relativi valori. Questo esempio illustra come modificare il tipo di contenuto, anche noto come tipo MIME.

```powershell
# populate the system properties
$cloudBlockBlob.FetchAttributes()

# view some of the system properties
# contentType is commonly referred to as MIME type
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
Write-Host "size = " $cloudBlockBlob.Properties.Length 

# change the content type to image/png
$contentType = "image/jpg"
$cloudBlockBlob.Properties.ContentType = $contentType 
$cloudBlockBlob.SetProperties() 

# get the system properties again to show that the content type has changed
$cloudBlockBlob.FetchAttributes()
Write-Host "content type = " $cloudBlockBlob.Properties.ContentType
```

Ogni BLOB è dotato di metadati che è possibile impostare. È possibile, ad esempio, archiviare il nome dell'utente che ha caricato il BLOB oppure la data e l'ora in cui è stato caricato la prima volta. I metadati sono costituiti da coppie chiave/valore. Questa impostazione può essere modificata tramite PowerShell, come indicato di seguito.

```powershell
# "filename" is the key, "original file name" is the value
$cloudBlockBlob.Metadata["filename"] = "original file name"

# "owner" is the key, "RobinS" is the value
$cloudBlockBlob.Metadata["owner"] = "RobinS"

# save the metadata and then display it
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata

# clear the metadata, save it, and show it
$cloudBlockBlob.Metadata.Clear()
$cloudBlockBlob.SetMetadata()
$cloudBlockBlob.Metadata
```

## <a name="managing-security-for-blobs"></a>Gestione della sicurezza dei BLOB 

Per impostazione predefinita, Archiviazione di Azure garantisce la sicurezza dei dati limitando l'accesso al proprietario dell'account, che possiede le chiavi di accesso dell'account di archiviazione. Quando è necessario condividere dati BLOB nell'account di archiviazione, è importante farlo senza compromettere la sicurezza delle chiavi di accesso dell'account. A tale scopo, è possibile usare un URL di firma di accesso condiviso, ovvero l'URL della risorsa che include i parametri di query e un token di sicurezza che consente uno specifico livello di autorizzazione per un determinato periodo di tempo. È ad esempio possibile concedere l'accesso in lettura a un BLOB privato per cinque minuti per consentire a un utente di visualizzarlo. 

### <a name="set-the-access-level-of-the-container-and-its-blobs-to-private"></a>Impostare su Privato il livello di accesso del contenitore e i relativi BLOB

Innanzitutto, impostare il livello di accesso del contenitore su `Off`, così che non sia possibile accedere senza una firma di accesso condiviso o la chiave dell'account. Usare [Set-AzureStorageContainerAcl](/powershell/module/azure.storage/Set-AzureStorageContainerAcl).

```powershell
Set-AzureStorageContainerAcl -Name $containerName -Context $ctx -Permission Off 
```

### <a name="test-private-access"></a>Verifica dell'accesso privato

Per verificare di non avere accesso ai BLOB nel contenitore, compilare l'URL in uno dei BLOB senza una firma di accesso condiviso e tentare di visualizzare il BLOB. Usando il protocollo HTTPS, l'URL avrà il formato seguente:

    `https://storageaccountname.blob.core.windows.net/containername/blobname`

Ciò mostra come creare l'URL del BLOB.

```powershell
$blobUrl = "https://" `
  $storageAccountName ".blob.core.windows.net/" + `
  $containerName +  "/" $blobName

Write-Host "Blob URL = " $blobUrl 
```

Copiare l'URL del BLOB e incollarlo in una finestra del browser privata; viene visualizzato un errore di autorizzazione, perché il BLOB è privato e non è stata inclusa la firma di accesso condiviso. 

### <a name="create-the-sas-uri"></a>Creare l'URI della firma di accesso condiviso

Creare un URI per la firma di accesso condiviso - Si tratta del collegamento al BLOB e include i parametri di query e un token di sicurezza che costituiscono la firma di accesso condiviso. Incollare l'URI in una finestra del browser privata. Viene visualizzata l'immagine. 

Creare innanzitutto la data e l'ora di inizio e la data e ora di scadenza dell'accesso. L'esempio usa una finestra di due minuti. 

```powershell
# set the start time to the current date/time 
# set the end time to 2 minutes in the future
$StartTime = Get-Date
$EndTime = $StartTime.AddMinutes(2.0)
```

Creare l'URI della firma di accesso condiviso con [New-AzureStorageBlobSASToken](/powershell/module/azure.storage/new-azurestorageblobsastoken). È necessario il nome del contenitore, il nome di BLOB, il contesto dell'account di archiviazione, le autorizzazioni da concedere (in questo caso, lettura, scrittura ed eliminazione) e l'ora di inizio e di fine dell'accesso. 

```powershell
$SASURI = New-AzureStorageBlobSASToken -Container $containerName `
    -Blob $blobName `
    -Context $ctx `
    -Permission "rwd" `
    -StartTime $StartTime `
    -ExpiryTime $EndTime `
    -FullUri

Write-Host "URL with SAS = " $SASURI
```

Copiare l'URI della firma di accesso condiviso e inserirlo in una finestra del browser privata. Viene visualizzata l'immagine.

Attendere il tempo necessario perché l'URL scada (in questo esempio due minuti), quindi passare all'URI in un'altra finestra del browser privata. Questa volta, si verificherà un errore di autorizzazione e non verrà visualizzata l'immagine, perché l'URI della firma di accesso condiviso è scaduto.

## <a name="clean-up-resources"></a>Pulire le risorse

Rimuovere tutte le risorse create. A tale scopo, è possibile rimuovere il gruppo di risorse, eliminando di conseguenza anche tutte le risorse che questo contiene. L'esempio rimuove l'account di archiviazione creato e il gruppo di risorse stesso.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono illustrate la creazione e la gestione di BLOB di base, ad esempio:

> [!div class="checklist"]
> * Creare un contenitore 
> * Caricare i BLOB
> * Elencare i BLOB in un contenitore 
> * Scaricare BLOB
> * Copiare i BLOB
> * Eliminare BLOB
> * Leggere e scrivere proprietà e metadati di un BLOB
> * Gestire la protezione tramite le firme di accesso condiviso

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Cmdlet di archiviazione per Microsoft Azure PowerShell
* [Cmdlet di PowerShell per l'archiviazione](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) è un'app autonoma gratuita di Microsoft che consente di rappresentare facilmente dati di Archiviazione di Azure in Windows, macOS e Linux.
