<properties 
	pageTitle="Come usare il servizio di archiviazione tabelle (Ruby) | Microsoft Azure" 
	description="Informazioni su come usare il servizio di archiviazione tabelle in Azure. Gli esempi di codice sono scritti usando l'API Ruby." 
	services="storage" 
	documentationCenter="ruby" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="tomfitz"/>





# Come usare il Servizio tabelle da Ruby

In questa guida verranno illustrati diversi scenari comuni di uso del servizio tabelle di Azure. Gli esempi sono scritti usando l'API Ruby. Gli scenari presentati includono la **creazione e l'eliminazione di una tabella, l'inserimento e l'esecuzione di query sulle entità in una tabella**. Per altre informazioni sulle tabelle, vedere la sezione [Passaggi successivi](#next-steps) .

## Sommario

* [Informazioni sul servizio tabelle](#what-is)
* [Concetti](#concepts)
* [Creare un account di archiviazione di Azure](#create-a-windows-azure-storage-account)
* [Creare un'applicazione Ruby](#create-a-ruby-application)
* [Configurare l'applicazione per l'accesso all'archiviazione](#configure-your-application-to-access-storage)
* [Configurare una connessione di archiviazione di Azure](#setup-a-windows-azure-storage-connection)
* [Procedura: Creare una tabella](#how-to-create-a-table)
* [Procedura: Aggiungere un'entità a una tabella](#how-to-add-an-entity-to-a-table)
* [Procedura: Aggiornare un'entità](#how-to-update-an-entity)
* [Procedura: Usare i gruppi di entità](#how-to-work-with-groups-of-entities)
* [Procedura: Eseguire query su un'entità](#how-to-query-for-an-entity)
* [Procedura: Eseguire query su un set di entità](#how-to-query-a-set-of-entities)
* [Procedura: Eseguire query su un subset di proprietà di entità](#how-to-query-a-subset-of-entity-properties)
* [Procedura: Eliminare un'entità](#how-to-delete-an-entity)
* [Procedura: Eliminare una tabella](#how-to-delete-a-table)
* [Passaggi successivi](#next-steps)

[AZURE.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

## <a id="create-a-windows-azure-storage-account"></a>Creare un account di archiviazione di Azure

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a id="create-a-ruby-application"></a>Creare un'applicazione Ruby

Creare un'applicazione Ruby. Per istruzioni, 
vedere l'articolo relativo alla [creazione di un'applicazione Ruby in Azure](/it-it/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a id="configure-your-application-to-access-storage"></a>Configurare l'applicazione per l'accesso all'archiviazione

Per usare l'archiviazione di Azure, è necessario scaricare e usare il pacchetto Ruby Azure, 
che comprende un set di librerie che comunicano con i servizi REST di archiviazione.

### Usare RubyGems per ottenere il pacchetto

1. Usare un'interfaccia della riga di comando, ad esempio **PowerShell** (Windows), **Terminale** (Mac) o **Bash** (Unix).

2. Digitare **gem install azure** nella finestra di comando per installare la gemma e le dipendenze.

### Importare il pacchetto

Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

	require "azure"

## <a id="setup-a-windows-azure-storage-connection"></a>Configurare una connessione di archiviazione di Azure

Il modulo di Azure leggerà le variabili di ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY** per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili non vengono impostate, è necessario specificare le informazioni relative all'account prima di usare **Azure::TableService** con il seguente codice:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"

Per ottenere questi valori:

1. Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/).

2. Passare all'account di archiviazione che si desidera usare.

3. Fare clic su **GESTISCI CHIAVI** nella parte inferiore del pannello di navigazione.

4. Nella finestra di dialogo popup saranno visualizzati il nome dell'account di archiviazione, la chiave di accesso primaria e la chiave di accesso secondaria. Per la chiave di accesso è possibile scegliere la primaria o la secondaria.

## <a id="how-to-create-a-table"></a>Procedura: Creare una tabella

L'oggetto **Azure::TableService** consente di usare tabelle ed entità. Per creare una tabella, usare il metodo **create\_table()**. Nell'esempio seguente viene creata una tabella o stampato l'eventuale errore.

	azure_table_service = Azure::TableService.new
	begin
	  azure_table_service.create_table("testtable")
	rescue
	  puts $!
	end

## <a id="how-to-add-an-entity-to-a-table"></a>Procedura: Aggiungere un'entità a una tabella

Per aggiungere un'entità, creare innanzitutto un oggetto hash che definisca le proprietà dell'entità. Si noti che per ogni entità è necessario specificare un oggetto **PartitionKey** e un oggetto **RowKey**. Si tratta di identificatori univoci dell'entità e sono valori che possono essere interrogati molto più velocemente di altre proprietà. Il servizio di archiviazione di Azure usa **PartitionKey** per distribuire automaticamente le entità della tabella su molti nodi di archiviazione. Le entità con lo stesso oggetto **PartitionKey**vengono archiviate nello stesso nodo. **RowKey** è l'ID univoco dell'entità all'interno della partizione cui appartiene. 

	entity = { "content" => "test entity", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.insert_entity("testtable", entity)

## <a id="how-to-update-an-entity"></a>Procedura: Aggiornare un'entità

Esistono vari metodi per aggiornare un'entità esistente:

* **update\_entity():** consente di aggiornare un'entità sostituendola.
* **merge\_entity():** consente di aggiornare un'entità esistente unendovi i nuovi valori di proprietà.
* **insert\_or\_merge\_entity():** consente di aggiornare un'entità esistente sostituendola. Se non esiste alcuna entità, ne verrà inserita una nuova:
* **insert\_or\_replace\_entity():** consente di aggiornare un'entità esistente unendovi i nuovi valori di proprietà. Se non esiste alcuna entità, ne verrà inserita una nuova.

Nel seguente esempio viene dimostrato l'aggiornamento di un'entità mediante l'uso di **update\_entity()**:

	entity = { "content" => "test entity with updated content", 
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.update_entity("testtable", entity)

Con **update\_entity()** e **merge\_entity()**, se l'entità da aggiornare non esiste, l'operazione di aggiornamento non riuscirà. Se quindi si desidera archiviare un'entità indipendentemente dal fatto che esista o meno, è necessario usare **insert\_or\_replace\_entity()** o **insert\_or\_merge\_entity()**.

## <a id="how-to-work-with-groups-of-entities"></a>Procedura: Usare i gruppi di entità

È talvolta consigliabile inviare più operazioni in un batch per garantire l'elaborazione atomica da parte del server. A tale scopo, è necessario prima creare un oggetto **Batch** e quindi usare il metodo **execute\_batch()** su **TableService**. Nell'esempio seguente viene dimostrato l'invio di due entità con RowKey 2 e 3 in un batch. Si noti che funziona solo per le entità con lo stesso oggetto PartitionKey.

	azure_table_service = Azure::TableService.new
	batch = Azure::Storage::Table::Batch.new("testtable", 
	  "test-partition-key") do
	  insert "2", { "content" => "new content 2" }
	  insert "3", { "content" => "new content 3" }
	end
	results = azure_table_service.execute_batch(batch)

## <a id="how-to-query-for-an-entity"></a>Procedura: Eseguire query su un'entità

Per eseguire query su un'entità in una tabella, usare il metodo **get\_entity()** passando il nome tabella e i parametri **PartitionKey** e **RowKey**.

	result = azure_table_service.get_entity("testtable", "test-partition-key", 
	  "1")

## <a id="how-to-query-a-set-of-entities"></a>Procedura: Eseguire query su un set di entità

Per eseguire query su un set di entità in una tabella, creare un oggetto hash di query e usare il metodo **query\_entities()**. Nell'esempio seguente viene dimostrato l'invio di tutte le entità con lo stesso oggetto **PartitionKey**:

	query = { :filter => "PartitionKey eq 'test-partition-key'" }
	result, token = azure_table_service.query_entities("testtable", query)

**Si noti** che se il set di risultati è troppo grande affinché sia restituito da un'unica query, verrà restituito un token di continuazione per recuperare le pagine successive.

## <a id="how-to-query-a-subset-of-entity-properties"></a>Procedura: Eseguire query su un subset di proprietà di entità

Mediante una query su una tabella è possibile recuperare solo alcune proprietà da un'entità. Questa tecnica, denominata "proiezione", consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. Usare la clausola select e passare i nomi delle proprietà da inoltrare al client.

	query = { :filter => "PartitionKey eq 'test-partition-key'", 
	  :select => ["content"] }
	result, token = azure_table_service.query_entities("testtable", query)

## <a id="how-to-delete-an-entity"></a>Procedura: Eliminare un'entità

Per eliminare un'entità, usare il metodo **delete\_entity()**. È necessario passare il nome della tabella contenente l'entità e i parametri PartitionKey e RowKey dell'entità.

		azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <a id="how-to-delete-a-table"></a>Procedura: Eliminare una tabella

Per eliminare una tabella, usare il metodo **delete\_table()** e passare il nome della tabella che si desidera eliminare.

		azure_table_service.delete_table("testtable")

## <a id="next-steps"></a>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione tabelle, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

* Vedere le informazioni di riferimento in MSDN: [Archiviazione](http://msdn.microsoft.com/library/windowsazure/gg433040.aspx)
* [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)
* Repository [Azure SDK for Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) su GitHub
<!--HONumber=42-->
