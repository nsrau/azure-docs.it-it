<properties
	pageTitle="Come usare l'archiviazione BLOB da Ruby | Microsoft Azure"
	description="Scoprire come usare l'archiviazione BLOB per caricare, scaricare, elencare ed eliminare contenuti BLOB. Gli esempi sono scritti in Ruby."
	services="storage"
	documentationCenter="ruby"
	authors="rmcmurray"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="robmcm"/>


# Come usare l'archiviazione BLOB da Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Panoramica

Questa guida illustra scenari comuni relativi all'uso dell'archiviazione BLOB. Gli esempi sono scritti utilizzando l'API Ruby. Gli scenari presentati includono **caricamento, visualizzazione dell'elenco, download** ed **eliminazione** di BLOB.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creare un'applicazione Ruby

Creare un'applicazione Ruby. Per istruzioni, vedere l'articolo relativo all'[applicazione Web Ruby on Rails in una macchina virtuale di Azure](../virtual-machines/virtual-machines-linux-classic-ruby-rails-web-app.md).

## Configurare l'applicazione per l'accesso all'archiviazione

Per usare l'archiviazione di Azure, è necessario scaricare e usare il pacchetto Ruby Azure, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### Utilizzare RubyGems per ottenere il pacchetto

1. Utilizzare un'interfaccia della riga di comando come **PowerShell** (Windows,) **Terminale** (Mac) o **Bash** (Unix).

2. Digitare "gem install azure" nella finestra di comando per installare la gemma e le dipendenze.

### Importare il pacchetto

Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

	require "azure"

## Configurare una connessione di archiviazione di Azure

Il modulo di Azure leggerà le variabili di ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY** per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account prima di usare **Azure::BLOB::BlobService** con il codice seguente:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Per ottenere questi valori da un account di archiviazione classico o ARM nel portale di Azure:

1. Accedere al [Portale di Azure](https://portal.azure.com).
2. Passare all'account di archiviazione che si desidera utilizzare.
3. Nel pannello Impostazioni a destra fare clic su **Chiavi di accesso**.
4. Nel pannello Chiavi di accesso visualizzato notare la chiave di accesso 1 e la chiave di accesso 2. È possibile usare una di queste indifferentemente. 
5. Fare clic sull'icona Copia per copiare la chiave negli Appunti. 

Per ottenere questi valori da un account di archiviazione classico nel portale classico:

1. Accedere al [portale classico](https://manage.windowsazure.com).
2. Passare all'account di archiviazione che si desidera utilizzare.
3. Fare clic su **GESTISCI CHIAVI DI ACCESSO** nella parte inferiore del riquadro di spostamento.
4. Nella finestra di dialogo popup saranno visualizzati il nome dell'account di archiviazione, la chiave di accesso primaria e la chiave di accesso secondaria. Per la chiave di accesso è possibile usare sia la chiave primaria che secondaria. 
5. Fare clic sull'icona Copia per copiare la chiave negli Appunti.

## Creare un contenitore

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

L'oggetto **Azure::BLOB::BlobService** consente di lavorare con contenitori e BLOB. Per creare un contenitore usare il metodo **create\_container()**.

Nell'esempio seguente viene creato un contenitore o stampato l'eventuale errore.

	azure_blob_service = Azure::Blob::BlobService.new
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

* **BLOB:** consente di specificare l'accesso in lettura pubblico completo per i dati di contenitori e BLOB. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

* **contenitore:** consente di specificare l'accesso in lettura pubblico per i BLOB. I dati BLOB all'interno di questo contenitore possono essere letti tramite richiesta anonima, ma i dati del contenitore non sono disponibili. I client non possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima.

In alternativa, è possibile modificare il livello di accesso di un contenitore usando il metodo **set\_container\_acl()** per specificare il livello di accesso pubblico.

Nell'esempio di codice seguente viene modificato il livello di accesso pubblico al **contenitore**:

	azure_blob_service.set_container_acl('test-container', "container")

## Caricare un BLOB in un contenitore

Per caricare contenuto in un BLOB, usare il metodo **create\_block\_blob()** per crearne uno, usando un file o una stringa come contenuto del BLOB.

Il codice seguente consentirà di caricare il file **test.png** come nuovo BLOB denominato "image-blob" nel contenitore.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## Elencare i BLOB in un contenitore

Per elencare i contenitori utilizzare il metodo **list\_containers()**. Per elencare i BLOB all'interno di un contenitore utilizzare il metodo **list\_blobs()**.

L'output sarà costituito dagli URL di tutti i BLOB in tutti i contenitori relativi all'account.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## Scaricare BLOB

Per scaricare i BLOB usare il metodo **get\_blob()** per recuperare il contenuto.

Nell'esempio seguente viene illustrato l'utilizzo di **get\_blob()** per scaricare il contenuto di "image-blob" e scriverlo in un file locale.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## Eliminare un BLOB
Per eliminare un BLOB, infine, usare il metodo **delete\_blob()**. Nell'esempio seguente viene illustrato come eliminare un BLOB.

	azure_blob_service.delete_blob(container.name, "image-blob")

## Passaggi successivi

Seguire i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

- [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Archivio [Azure SDK per Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) su GitHub
- [Trasferire dati con l'utilità della riga di comando AzCopy](storage-use-azcopy.md)

<!---HONumber=AcomDC_0323_2016-->