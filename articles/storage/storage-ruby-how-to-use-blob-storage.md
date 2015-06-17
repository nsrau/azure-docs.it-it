<properties 
	pageTitle="Come usare l'archiviazione BLOB da Ruby | Microsoft Azure" 
	description="Informazioni su come usare il servizio BLOB di Azure per caricare, scaricare, elencare ed eliminare contenuti BLOB. Gli esempi sono scritti in Ruby." 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac,tamram" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="tomfitz"/>


# Come usare l'archiviazione BLOB da Ruby

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Informazioni generali

Questa guida illustra diversi scenari d'uso comuni del
servizio BLOB di Azure. Gli esempi sono scritti usando l'API Ruby.
Gli scenari presentati includono **caricamento, visualizzazione in elenchi, download** ed **eliminazione** di BLOB.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creare un'applicazione Ruby

Creare un'applicazione Ruby. Per istruzioni, 
vedere l'articolo [Creare un'applicazione Ruby in Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Configurare l'applicazione per l'accesso all'archiviazione

Per usare l'archiviazione di Azure, è necessario scaricare e usare il pacchetto Ruby Azure, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### Usare RubyGems per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **Terminale** (Mac) o **Bash** (Unix).

2. Digitare "gem install azure" nella finestra di comando per installare la gemma e le dipendenze.

### Importare il pacchetto

Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

	require "azure"

## Configurare una connessione di archiviazione di Azure

Il modulo azure leggerà le variabili di ambiente **AZURE_STORAGE_ACCOUNT** e **AZURE_STORAGE_ACCESS_KEY** 
per ottenere le informazioni necessarie per connettersi all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account prima di usare **Azure::BlobService** con il codice seguente:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"


Per ottenere questi valori:

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/).
2. Passare all'account di archiviazione che si desidera usare
3. Fare clic su **GESTISCI CHIAVI** nella parte inferiore del pannello di navigazione.
4. Nella finestra di dialogo popup saranno visualizzati il nome dell'account di archiviazione, la chiave di accesso primaria e la chiave di accesso secondaria. Per la chiave di accesso è possibile usare sia la chiave primaria che secondaria.

## Procedura: Creare un contenitore

L'oggetto **Azure::BlobService** consente di usare contenitori e BLOB. Per creare un argomento, usare il metodo **create_container()**.

Nell'esempio seguente viene creato un contenitore o stampato l'eventuale errore.

	azure_blob_service = Azure::BlobService.new
	begin
	  container = azure_blob_service.create_container("test-container")
	rescue
	  puts $!
	end

Se si desidera rendere pubblici i file nel contenitore è possibile impostare le autorizzazioni del contenitore. 

È possibile modificare solo la chiamata <strong>create_container()</strong> per passare l'opzione **:public_access_level**:

	container = azure_blob_service.create_container("test-container", 
	  :public_access_level => "<public access level>")


I valori validi per l'opzione **:public_access_level** sono:

* **blob:** consente di specificare l'accesso in lettura pubblico completo per i dati di contenitori e BLOB. I client possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima, ma non sono in grado di enumerare i contenitori all'interno dell'account di archiviazione.

* **container:** consente di specificare l'accesso in lettura pubblico per i BLOB. I dati BLOB all'interno di questo contenitore possono essere letti tramite richiesta anonima, ma i dati del contenitore non sono disponibili. I client non possono enumerare i BLOB all'interno del contenitore tramite richiesta anonima.

In alternativa, è possibile modificare il livello di accesso pubblico di un contenitore usando il metodo **set_container_acl()** per specificare il livello di accesso pubblico.
 
Nell'esempio seguente viene illustrata la modifica del livello di accesso pubblico al **contenitore**:

	azure_blob_service.set_container_acl('test-container', "container")

## Procedura: Caricare un BLOB in un contenitore

Per caricare contenuto in un BLOB, usare il metodo **create_block_blob()** per crearne uno, usando un file o una stringa come contenuto del BLOB. 

Il seguente codice consentirà di caricare il file **test.png** come nuovo BLOB denominato "image-blob" nel contenitore.

	content = File.open("test.png", "rb") { |file| file.read }
	blob = azure_blob_service.create_block_blob(container.name,
	  "image-blob", content)
	puts blob.name

## Procedura: Elencare i BLOB in un contenitore

Per elencare i contenitori, usare il metodo **list_containers()**. 
Per elencare i BLOB all'interno di un contenitore usare il metodo **list_blobs()**. 

L'output sarà costituito dagli URL di tutti i BLOB in tutti i contenitori relativi all'account.

	containers = azure_blob_service.list_containers()
	containers.each do |container|
	  blobs = azure_blob_service.list_blobs(container.name)
	  blobs.each do |blob|
	    puts blob.name
	  end
	end

## Procedura: Scaricare BLOB

Per scaricare i BLOB usare il metodo **get_blob()** per recuperare il contenuto. 

Nell'esempio seguente viene illustrato l'uso di **get_blob()** per scaricare il contenuto di "image-blob" e scriverlo in un file locale.

	blob, content = azure_blob_service.get_blob(container.name,"image-blob")
	File.open("download.png","wb") {|f| f.write(content)}

## Procedura: Eliminare un BLOB
Per eliminare un BLOB, infine, usare il metodo **delete_blob()**. Nell'esempio seguente viene illustrato come eliminare un BLOB.

	azure_blob_service.delete_blob(container.name, "image-blob")

## Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione BLOB, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

- Vedere le informazioni di riferimento in MSDN: [Archiviazione di Azure](http://msdn.microsoft.com/library/azure/gg433040.aspx)
- [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Repository [Azure SDK per Ruby](https://github.com/WindowsAzure/azure-sdk-for-ruby) su GitHub

<!--HONumber=49--> 