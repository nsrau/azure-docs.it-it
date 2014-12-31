<properties urlDisplayName="Blob Service" pageTitle="Come usare il servizio di archiviazione BLOB (Ruby) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Ruby" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Blob Service from Ruby" authors="tomfitz" solutions="" manager="wpickett" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="11/21/2014" ms.author="tomfitz" />





#Come usare il servizio BLOB da Ruby
 In questa guida verranno illustrati diversi scenari comuni di uso del servizio BLOB di Azure. Gli esempi sono scritti usando l'API Ruby. Gli scenari presentati includono **caricamento, visualizzazione dell'elenco, download** ed **eliminazione** di BLOB. Per altre informazioni sui BLOB, vedere la sezione [Passaggi successivi](#next-steps).

##Sommario

* [Informazioni sul servizio BLOB](#what-is)
* [Concetti](#concepts)
* [Creare un account di archiviazione di Azure](#CreateAccount)
* [Creare un'applicazione Ruby](#CreateRubyApp)
* [Configurare l'applicazione per l'accesso all'archiviazione](#ConfigAccessStorage)
* [Configurare una connessione di archiviazione di Azure](#SetupStorageConnection)
* [Procedura: Creare un contenitore](#CreateContainer)
* [Procedura: Caricare un BLOB in un contenitore](#UploadBlob)
* [Procedura: Elencare i BLOB in un contenitore](#ListBlobs)
* [Procedura: Scaricare BLOB](#DownloadBlobs)
* [Procedura: Eliminare un BLOB](#DeleteBlob)
* [Passaggi successivi](#NextSteps)


[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a id="CreateAccount"></a>Creare un account di archiviazione di Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a id="CreateRubyApp"></a>Creare un'applicazione Ruby

Creare un'applicazione Ruby. Per istruzioni,vedere [Creazione di un'applicazione Ruby in Azure](/it-it/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="ConfigAccessStorage"></a>Configurare l'applicazione per l'accesso all'archiviazione

Per usare l'archiviazione di Azure, è necessario scaricare e usare il pacchetto Ruby Azure, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### Usare RubyGems per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando come **PowerShell** (Windows), **Terminale** (Mac) o **Bash** (Unix).

2. Digitare "gem install azure" nella finestra di comando per installare la gemma e le dipendenze.

### Importare il pacchetto

Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

	require "azure"

## <a id="SetupStorageConnection"></a>Configurare una connessione di archiviazione di Azure

Il modulo azure leggerà le variabili di ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS_KEY** 
per ottenere le informazioni necessarie per connettersi all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account prima di usare **Azure::BlobService** con il codice seguente:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Per ottenere questi valori:

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/).
2. Passare all'account di archiviazione che si desidera usare
3. Fare clic su **GESTISCI CHIAVI** nella parte inferiore del pannello di navigazione.
4. Nella finestra di dialogo popup saranno visualizzati il nome dell'account di archiviazione, la chiave di accesso primaria e la chiave di accesso secondaria. Per la chiave di accesso è possibile usare sia la chiave primaria che secondaria.

## <a id="CreateContainer"></a>Procedura: Creare un contenitore

L'oggetto **Azure::BlobService** consente di usare contenitori e BLOB. Per creare un argomento, usare il metodo **create\_container()**.

Nell'esempio seguente viene creato un contenitore o stampato l'eventuale errore.

	azure_blob_service = Azure::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

Se si desidera rendere pubblici i file nel contenitore è possibile impostare le autorizzazioni del contenitore. 

È possibile modificare solo la chiamata <strong>create\_container()</strong> per passare l'opzione **:public\_access\_level**:

	container = azure_blob_service.create_container("test-container", 
	  :public_access_level => "<public access level>")


I valori validi per l'opzione **:public\_access\_level** sono:

* **blob:** consente di specificare l'accesso in lettura pubblico completo per i dati di contenitori e BLOB. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

* **container:** consente di specificare l'accesso in lettura pubblico per i BLOB. I dati BLOB all'interno di questo contenitore possono essere letti tramite richiesta anonima, ma i dati del contenitore non sono disponibili. I client non possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima.

In alternativa, è possibile modificare il livello di accesso pubblico di un contenitore usando il metodo **set\_container\_acl()** per specificare il livello di accesso pubblico.
 
Nell'esempio seguente viene illustrata la modifica del livello di accesso pubblico al **contenitore**:

	azure_blob_service.set_container_acl('test-container', "container")

## <a id="UploadBlob"></a>Procedura: Caricare un BLOB in un contenitore

Per caricare contenuto in un BLOB, usare il metodo **create\_block\_blob()** per crearne uno, usando un file o una stringa come contenuto del BLOB. 

Il codice seguente consentirà di caricare il file **test.png** come nuovo BLOB denominato "image-blob" nel contenitore.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## <a id="ListBlobs"></a>Procedura: Elencare i BLOB in un contenitore

Per elencare i contenitori usare il metodo **list_containers()**. 
Per elencare i BLOB all'interno di un contenitore usare il metodo **list\_blobs()**. 

L'output sarà costituito dagli URL di tutti i BLOB in tutti i contenitori relativi all'account.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## <a id="DownloadBlobs"></a>Procedura: Scaricare BLOB

Per scaricare i BLOB usare il metodo **get\_blob()** per recuperare il contenuto. 

Nell'esempio seguente viene illustrato l'uso di **get\_blob()** per scaricare il contenuto di "image-blob" e scriverlo in un file locale.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## <a id="DeleteBlob"></a>Procedura: Eliminare un BLOB
Per eliminare un BLOB, infine, usare il metodo **delete\_blob()**. Nell'esempio seguente viene illustrato come eliminare un BLOB.

	azure_blob_service.delete_blob(container.name, "image-blob")

## <a id="NextSteps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

-   Vedere le informazioni di riferimento in MSDN: [Archiviazione e accesso ai dati in Azure](http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx)
-   [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)
-   Repository [Azure SDK per Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) su GitHub

<!--HONumber=35_1-->
