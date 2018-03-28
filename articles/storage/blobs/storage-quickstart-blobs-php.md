---
title: Guida introduttiva di Azure - Trasferire oggetti da e verso un archivio BLOB di Azure con PHP | Microsoft Docs
description: Imparare velocemente come trasferire oggetti da e verso un archivio BLOB di Azure con PHP
services: storage
author: roygara
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: quickstart
ms.date: 03/15/2018
ms.author: rogarana
ms.openlocfilehash: 4adad6fe3da16653bbd654a3e93e14f9e68b7c90
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
#  <a name="transfer-objects-tofrom-azure-blob-storage-using-php"></a>Trasferire oggetti da e verso un archivio BLOB di Azure con PHP
Questa guida introduttiva illustra come usare PHP per caricare, scaricare ed elencare BLOB in blocchi in un contenitore in un archivio BLOB di Azure. 

## <a name="prerequisites"></a>prerequisiti

Per completare questa guida introduttiva: 
* [Installare PHP](http://php.net/downloads.php)
* Installare [Azure Storage SDK per PHP](https://github.com/Azure/azure-storage-php)


Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

## <a name="download-the-sample-application"></a>Scaricare l'applicazione di esempio
L'[applicazione di esempio](https://github.com/Azure-Samples/storage-blobs-php-quickstart.git) usata in questa guida introduttiva è un'applicazione PHP di base.  

Usare [git](https://git-scm.com/) per scaricare una copia dell'applicazione nell'ambiente di sviluppo. 

```bash
git clone https://github.com/Azure-Samples/storage-blobs-php-quickstart.git
```

Questo comando consente di duplicare il repository nella cartella locale git. Per aprire l'applicazione PHP di esempio, cercare la cartella storage-blobs-php-quickstart e aprire il file phpqs.php.  

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione
Nell'applicazione è necessario specificare il nome dell'account di archiviazione e la chiave dell'account per creare l'istanza di **BlobRestProxy** per l'applicazione. È consigliabile archiviare questi identificatori in una variabile di ambiente nel computer locale che esegue l'applicazione. Per creare la variabile di ambiente usare uno degli esempi riportati di seguito, in base al sistema operativo in uso. Sostituire i valori **youraccountname** e **youraccountkey** con il nome e la chiave dell'account.

# <a name="linux-tablinux"></a>[Linux] (#tab/linux)

```bash
export ACCOUNT_NAME=<youraccountname>
export ACCOUNT_KEY=<youraccountkey>
```

# <a name="windows-tabwindows"></a>[Windows] (#tab/windows)

```cmd
setx ACCOUNT_NAME=<youraccountname>
setx ACCOUNT_KEY=<youraccountkey>
```
---

## <a name="configure-your-environment"></a>Configurare l'ambiente
Spostare la cartella dalla cartella git locale a una directory gestita dal server PHP. Aprire quindi un prompt dei comandi con tale directory come ambito e immettere `php composer.phar install`

## <a name="run-the-sample"></a>Eseguire l'esempio
Questo esempio crea un file di test nella cartella ".". Il programma di esempio consente di caricare il file di test nell'archiviazione BLOB, elencare i BLOB nel contenitore, quindi scaricare il file con un nuovo nome. 

Eseguire l'esempio. Il seguente output è un esempio dell'output ricevuto durante l'esecuzione dell'applicazione:
  
```
Uploading BlockBlob: HelloWorld.txt
These are the blobs present in the container: HelloWorld.txt: https://myexamplesacct.blob.core.windows.net/blockblobsleqvxd/HelloWorld.txt

This is the content of the blob uploaded: Hello Azure!
```
Facendo clic sul pulsante visualizzato, il programma di esempio elimina il contenitore di archiviazione e i file. Prima di continuare, controllare che nella cartella del server siano presenti i due file. È possibile aprirli e vedere che sono identici.

È anche possibile usare uno strumento come [Azure Storage Explorer](http://storageexplorer.com) per visualizzare i file nell'archiviazione BLOB. Azure Storage Explorer è uno strumento multipiattaforma gratuito che consente di accedere alle informazioni dell'account di archiviazione. 

Dopo aver verificato i file, premere un tasto qualsiasi per terminare la demo ed eliminare i file di test. Ora che si conosce il risultato dell'esempio, aprire il file example.rb per esaminare il codice. 

## <a name="understand-the-sample-code"></a>Informazioni sul codice di esempio

Viene quindi descritto in dettaglio il codice di esempio, per consentire di comprenderne il funzionamento.

### <a name="get-references-to-the-storage-objects"></a>Ottenere i riferimenti agli oggetti di archiviazione
La prima cosa da fare è creare i riferimenti agli oggetti usati per accedere e gestire l'archiviazione BLOB. Questi oggetti si compilano a vicenda: ognuno di essi viene usato da quello successivo nell'elenco.

* Creare un'istanza dell'oggetto **BlobRestProxy** di Archiviazione di Azure per configurare le credenziali di connessione. 
* Creare l'oggetto **BlobService** che fa riferimento al servizio BLOB nell'account di archiviazione. 
* Creare l'oggetto **Container** che rappresenta il contenitore a cui si accede. I contenitori vengono usati per organizzare i BLOB, così come si usano le cartelle nel computer per organizzare i file.

Dopo aver creato l'oggetto contenitore **blobClient**, è possibile creare l'oggetto BLOB **Block** che punta al BLOB specifico a cui si è interessati. Si possono quindi eseguire operazioni come caricamento, download e copia.

> [!IMPORTANT]
> I nomi dei contenitori devono essere in minuscolo. Per altre informazioni sulla denominazione dei contenitori e dei BLOB vedere [Naming and Referencing Containers, Blobs, and Metadata](https://docs.microsoft.com/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) (Denominazione e riferimento a contenitori, BLOB e metadati).

In questa sezione si configura un'istanza del client di archiviazione di Azure, si crea un'istanza dell'oggetto del servizio BLOB, si crea un nuovo contenitore e si impostano le autorizzazioni per il contenitore in modo che i BLOB siano pubblici. Il contenitore è denominato **quickstartblobs**. 

```PHP
    # Setup a specific instance of an Azure::Storage::Client
    $connectionString = "DefaultEndpointsProtocol=https;AccountName=".getenv('account_name').";AccountKey=".getenv('account_key');
    
    // Create blob client.
    $blobClient = BlobRestProxy::createBlobService($connectionString);
    
    # Create the BlobService that represents the Blob service for the storage account
    $createContainerOptions = new CreateContainerOptions();
    
    $createContainerOptions->setPublicAccess(PublicAccessType::CONTAINER_AND_BLOBS);
    
    // Set container metadata.
    $createContainerOptions->addMetaData("key1", "value1");
    $createContainerOptions->addMetaData("key2", "value2");

    $containerName = "blockblobs".generateRandomString();

    try    {
        // Create container.
        $blobClient->createContainer($containerName, $createContainerOptions);
```

### <a name="upload-blobs-to-the-container"></a>Caricare i BLOB nel contenitore

L'archiviazione BLOB supporta BLOB in blocchi, BLOB di aggiunta e BLOB di pagine. I BLOB in blocchi sono quelli usati più di frequente e vengono usati in questa guida rapida.  

Per caricare un file in un BLOB, ottenere il percorso completo del file unendo il nome della directory e il nome del file nell'unità locale. È quindi possibile caricare il file nel percorso specificato con il metodo **createBlockBlob()**. 

Il codice di esempio carica un file locale in Azure. Nel codice, il file è archiviato come **myfile** e il nome del BLOB come **fileToUpload**. Nell'esempio seguente il file viene caricato in un contenitore denominato **quickstartblobs**.

```PHP
    $myfile = fopen("HelloWorld.txt", "w") or die("Unable to open file!");
    fclose($myfile);

    # Upload file as a block blob
    echo "Uploading BlockBlob: ".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    
    $content = fopen($fileToUpload, "r");

    //Upload blob
    $blobClient->createBlockBlob($containerName, $fileToUpload, $content);
```

Per eseguire un aggiornamento parziale del contenuto di un BLOB in blocchi, usare il metodo **createblocklist()**. I BLOB in blocchi possono avere dimensioni pari a 4,7 TB e possono essere qualsiasi tipo di file, da fogli di calcolo di Excel ai file video di grandi dimensioni. I BLOB di pagine vengono usati principalmente per i file VHD usati per tornare alle macchine virtuali IaaS. I BLOB di accodamento sono usati per la registrazione, ad esempio quando si vuole scrivere in un file e poi continuare ad aggiungere altre informazioni. Il BLOB di accodamento deve essere usato in un modello a singolo writer. La maggior parte degli oggetti presenti nell'archiviazione BLOB è costituita da BLOB in blocchi.

### <a name="list-the-blobs-in-a-container"></a>Elencare i BLOB in un contenitore

È possibile ottenere un elenco dei file nel contenitore con il metodo **listBlobs()**. Il codice seguente recupera l'elenco di BLOB, quindi esegue il ciclo per tutti loro mostrando i nomi dei BLOB trovati in un contenitore.  

```PHP
    $listBlobsOptions = new ListBlobsOptions();
    $listBlobsOptions->setPrefix("HelloWorld");
    
    echo "These are the blobs present in the container: ";
    
    do{
        $result = $blobClient->listBlobs($containerName, $listBlobsOptions);
        foreach ($result->getBlobs() as $blob)
        {
            echo $blob->getName().": ".$blob->getUrl()."<br />";
        }
        
        $listBlobsOptions->setContinuationToken($result->getContinuationToken());
    } while($result->getContinuationToken());
```

### <a name="get-the-content-of-your-blobs"></a>Ottenere il contenuto dei BLOB

Ottenere il contenuto dei BLOB con il metodo **getBlob()**. Il codice seguente visualizza il contenuto del BLOB caricato in una sezione precedente.

```PHP
    $blob = $blobClient->getBlob($containerName, $fileToUpload);
    fpassthru($blob->getContentStream());
```

### <a name="clean-up-resources"></a>Pulire le risorse
Se i BLOB caricati in questa guida introduttiva non sono più necessari, è possibile eliminare l'intero contenitore con il metodo **deleteContainer()**. Se i file creati non sono più necessari, usare il metodo **deleteBlob()** per eliminarli.

```PHP
    // Delete blob.
    echo "Deleting Blob".PHP_EOL;
    echo $fileToUpload;
    echo "<br />";
    $blobClient->deleteBlob($_GET["containerName"], $fileToUpload);

    // Delete container.
    echo "Deleting Container".PHP_EOL;
    echo $_GET["containerName"].PHP_EOL;
    echo "<br />";
    $blobClient->deleteContainer($_GET["containerName"]);

    //Deleting local file
    echo "Deleting file".PHP_EOL;
    echo "<br />";
    unlink($fileToUpload);   
```

## <a name="resources-for-developing-php-applications-with-blobs"></a>Risorse per lo sviluppo di applicazioni PHP con BLOB

Per lo sviluppo PHP con Archiviazione BLOB, vedere le risorse aggiuntive seguenti:

- Visualizzare, scaricare e installare il [codice sorgente della libreria client PHP](https://github.com/Azure/azure-storage-php) per Archiviazione di Azure su GitHub.
- Esplorare gli [esempi per Archiviazione BLOB](https://azure.microsoft.com/resources/samples/?sort=0&service=storage&platform=php&term=blob) scritti con la libreria client PHP.

## <a name="next-steps"></a>Passaggi successivi
 
In questa guida introduttiva si è appreso come trasferire file tra un disco locale e un archivio BLOB di Azure con PHP. Per altre informazioni sull'uso dell'archiviazione BLOB, continuare la procedura relativa all'archiviazione BLOB.

> [!div class="nextstepaction"]
> [Procedura relativa alle operazioni di archiviazione BLOB](./storage-php-how-to-use-blobs.md)


Per altre informazioni su Azure Storage Explorer e i BLOB, vedere [Gestire le risorse dell'archivio BLOB di Azure con Storage Explorer](../../vs-azure-tools-storage-explorer-blobs.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).
