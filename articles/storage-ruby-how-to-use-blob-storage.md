<properties linkid="dev-ruby-how-to-blob-storage" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Ruby) | Microsoft Azure" metaKeywords="Get started Azure blob, Azure unstructured data, Azure unstructured storage, Azure blob, Azure blob storage, Azure blob Ruby" description="Learn how to use the Azure blob service to upload, download, list, and delete blob content. Samples written in Ruby." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Blob Service from Ruby" authors="guayan" solutions="" manager="" editor="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="ruby" ms.topic="article" ms.date="01/01/1900" ms.author="guayan" />

# Come utilizzare il servizio BLOB da Ruby

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio BLOB di
Azure. Gli esempi sono scritti usando l'API Ruby.
Gli scenari illustrati includono il **caricamento, la visualizzazione di un elenco, il download** e l'**eliminazione** di BLOB.
Per altre informazioni sui BLOB, vedere la sezione [Passaggi successivi][Passaggi successivi].

## Sommario

-   [Informazioni sul servizio BLOB][Informazioni sul servizio BLOB]
-   [Concetti][Concetti]
-   [Creazione di un account di archiviazione di Azure][Creazione di un account di archiviazione di Azure]
-   [Creazione di un'applicazione Ruby][Creazione di un'applicazione Ruby]
-   [Configurazione dell'applicazione per l'accesso all'archiviazione][Configurazione dell'applicazione per l'accesso all'archiviazione]
-   [Configurazione di una connessione di archiviazione di Azure][Configurazione di una connessione di archiviazione di Azure]
-   [Procedura: Creare un contenitore][Procedura: Creare un contenitore]
-   [Procedura: Caricare un BLOB in un contenitore][Procedura: Caricare un BLOB in un contenitore]
-   [Procedura: Elencare i BLOB in un contenitore][Procedura: Elencare i BLOB in un contenitore]
-   [Procedura: Scaricare BLOB][Procedura: Scaricare BLOB]
-   [Procedura: Eliminare un BLOB][Procedura: Eliminare un BLOB]
-   [Passaggi successivi][1]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <span id="CreateAccount"></span></a>Creazione di un account di archiviazione di Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <span id="CreateRubyApp"></span></a>Creazione di un'applicazione Ruby

Creare un'applicazione Ruby. Per istruzioni,
vedere [Creazione di un'applicazione Ruby in Azure][Creazione di un'applicazione Ruby in Azure].

## <span id="ConfigAccessStorage"></span></a>Configurazione dell'applicazione per l'accesso all'archiviazione

Per utilizzare l'archiviazione di Azure, è necessario scaricare e utilizzare il pacchetto Ruby Azure, che comprende un set di librerie che comunicano con i servizi di archiviazione REST.

### Utilizzare RubyGems per ottenere il pacchetto

1.  Utilizzare un'interfaccia della riga di comando come **PowerShell** (Windows,) **Terminale** (Mac) o **Bash** (Unix).

2.  Digitare "gem install azure" nella finestra di comando per installare la gemma e le dipendenze.

### Importare il pacchetto

Utilizzando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende utilizzare l'archiviazione:

    require "azure"

## <span id="SetupStorageConnection"></span></a>Configurazione di una connessione di archiviazione di Azure

Il modulo di Azure leggerà le variabili di ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY**
 per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account prima di utilizzare **Azure::BlobService** con il codice seguente:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

Per ottenere questi valori:

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].
2.  Passare all'account di archiviazione che si desidera utilizzare
3.  Fare clic su **MANAGE KEYS** nella parte inferiore del pannello di navigazione.
4.  Nella finestra di dialogo popup saranno visualizzati il nome dell'account di archiviazione, la chiave di accesso primaria e la chiave di accesso secondaria. Per la chiave di accesso è possibile scegliere la primaria o la secondaria.

## <span id="CreateContainer"></span></a>Procedura: Creare un contenitore

L'oggetto **Azure::BlobService** consente di lavorare con contenitori e BLOB. Per creare un contenitore utilizzare il metodo **create\_container()**.

Nell'esempio seguente viene creato un contenitore o stampato l'eventuale errore.

    azure_blob_service = Azure::BlobService.new
    begin
      container = azure_blob_service.create_container("test-container")
    rescue
      puts $!
    end

Se si desidera rendere pubblici i file nel contenitore è possibile impostare le autorizzazioni del contenitore.

È possibile modificare solo la chiamata **create\_container()** in modo da passare l'opzione **:public\_access\_level**:

    container = azure_blob_service.create_container("test-container", 
      :public_access_level => "<public access level>")

I valori validi per l'opzione **:public\_access\_level** sono:

-   **blob:** consente di specificare l'accesso in lettura pubblico completo per i dati di contenitori e BLOB. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

-   **container:** consente di specificare l'accesso in lettura pubblico per i BLOB. I dati BLOB all'interno di questo contenitore possono essere letti tramite richiesta anonima, ma i dati del contenitore non sono disponibili. I client non possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima.

In alternativa, è possibile modificare il livello di accesso di un contenitore utilizzando il metodo **set\_container\_acl()** per specificare il livello di accesso pubblico.

Nell'esempio seguente viene illustrata la modifica del livello di accesso pubblico al **contenitore**:

    azure_blob_service.set_container_acl('test-container', "container")

## <span id="UploadBlob"></span></a>Procedura: Caricare un BLOB in un contenitore

Per caricare contenuto in un BLOB, utilizzare il metodo **create\_block\_blob()** per crearne uno, utilizzando un file o una stringa come contenuto del BLOB.

Il codice seguente consentirà di caricare il file **test.png** come nuovo BLOB denominato "image-blob" nel contenitore.

    content = File.open("test.png", "rb") { |file| file.read }
    blob = azure_blob_service.create_block_blob(container.name,
      "image-blob", content)
    puts blob.name

## <span id="ListBlobs"></span></a>Procedura: Elencare i BLOB in un contenitore

Per visualizzare un elenco dei contenitori, usare il metodo **list\_containers()**.
Per visualizzare un elenco dei BLOB all'interno di un contenitore usare il metodo **list\_blobs()**.

L'output sarà costituito dagli URL di tutti i BLOB in tutti i contenitori relativi all'account.

    containers = azure_blob_service.list_containers()
    containers.each do |container|
      blobs = azure_blob_service.list_blobs(container.name)
      blobs.each do |blob|
        puts blob.name
      end
    end

## <span id="DownloadBlobs"></span></a>Procedura: Scaricare BLOB

Per scaricare i BLOB utilizzare il metodo **get\_blob()** per recuperare il contenuto.

Nell'esempio seguente viene illustrato l'utilizzo di **get\_blob()** per scaricare il contenuto di "image-blob" e scriverlo in un file locale.

    blob, content = azure_blob_service.get_blob(container.name,"image-blob")
    File.open("download.png","wb") {|f| f.write(content)}

## <span id="DeleteBlob"></span></a>Procedura: Eliminare un BLOB

Per eliminare un BLOB, infine, utilizzare il metodo **delete\_blob()**. Nell'esempio seguente viene illustrato come eliminare un BLOB.

    azure_blob_service.delete_blob(container.name, "image-blob")

## <span id="NextSteps"></span></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure][Archiviazione e accesso ai dati in Azure]
-   [Blog del team di Archiviazione di Azure][Blog del team di Archiviazione di Azure]
-   Archivio [Azure SDK for Ruby][Azure SDK for Ruby] su GitHub

  [Passaggi successivi]: #next-steps
  [Informazioni sul servizio BLOB]: #what-is
  [Concetti]: #concepts
  [Creazione di un account di archiviazione di Azure]: #CreateAccount
  [Creazione di un'applicazione Ruby]: #CreateRubyApp
  [Configurazione dell'applicazione per l'accesso all'archiviazione]: #ConfigAccessStorage
  [Configurazione di una connessione di archiviazione di Azure]: #SetupStorageConnection
  [Procedura: Creare un contenitore]: #CreateContainer
  [Procedura: Caricare un BLOB in un contenitore]: #UploadBlob
  [Procedura: Elencare i BLOB in un contenitore]: #ListBlobs
  [Procedura: Scaricare BLOB]: #DownloadBlobs
  [Procedura: Eliminare un BLOB]: #DeleteBlob
  [1]: #NextSteps
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [Creazione di un'applicazione Ruby in Azure]: /it-it/develop/ruby/tutorials/web-app-with-linux-vm/
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [Archiviazione e accesso ai dati in Azure]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Azure SDK for Ruby]: https://github.com/WindowsAzure/azure-sdk-for-ruby
