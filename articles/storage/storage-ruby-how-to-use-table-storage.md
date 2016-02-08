<properties
	pageTitle="Come usare l'archiviazione tabelle di Azure da Ruby | Microsoft Azure"
	description="Informazioni su come usare l'archiviazione tabelle di Azure. Gli esempi di codice sono scritti usando l'API Ruby."
	services="storage"
	documentationCenter="ruby"
	authors="tfitzmac"
	manager="wpickett"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="tomfitz"/>


# Come usare l'archiviazione tabelle di Azure da Ruby

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

## Panoramica

Questa guida illustra come eseguire scenari comuni con il servizio tabelle di Azure. Gli esempi sono scritti utilizzando l'API Ruby. Gli scenari presentati includono **creazione ed eliminazione di una tabella, inserimento di entità ed esecuzione di query sulle entità in una tabella**.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Creare un'applicazione Ruby

Per istruzioni su come creare un'applicazione Ruby, vedere [creare un'applicazione Ruby in Azure](/develop/ruby/tutorials/web-app-with-linux-vm/).

## Configurare l'applicazione per l'accesso all'archiviazione

Per usare l'archiviazione di Azure, è necessario scaricare e usare il pacchetto Ruby Azure, che comprende un set di pratiche librerie che comunicano con i servizi di archiviazione REST.

### Utilizzare RubyGems per ottenere il pacchetto

1. Utilizzare un'interfaccia della riga di comando come **PowerShell** (Windows,) **Terminale** (Mac) o **Bash** (Unix).

2. Digitare **gem install azure** nella finestra di comando per installare la gemma e le dipendenze.

### Importare il pacchetto

Usando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende usare l'archiviazione:

	require "azure"

## Configurare una connessione di archiviazione di Azure

Il modulo di Azure leggerà le variabili di ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY** per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account prima di utilizzare **Azure::TableService** con il codice seguente:

	Azure.config.storage_account_name = "<your azure storage account>"
	Azure.config.storage_access_key = "<your azure storage access key>"

Per ottenere questi valori:

1. Accedere al [Portale di Azure](https://portal.azure.com).

2. Passare all'account di archiviazione.

3. Nel pannello **Impostazioni**, selezionare **Chiavi**.

4. Copiare il valore della chiave di accesso desiderata.

## Creare una tabella

L'oggetto **Azure::TableService** consente di utilizzare tabelle ed entità. Per creare una tabella utilizzare il metodo **create\_table()**. Nell'esempio seguente viene creata una tabella o stampato l'eventuale errore.

	azure_table_service = Azure::TableService.new
	begin
	  azure_table_service.create_table("testtable")
	rescue
	  puts $!
	end

## Aggiungere un'entità a una tabella

Per aggiungere un'entità, creare innanzitutto un oggetto hash che definisca le proprietà dell'entità. Si noti che per ogni entità è necessario specificare un oggetto **PartitionKey** e un oggetto **RowKey**. Si tratta di identificatori univoci dell'entità e sono valori che possono essere interrogati molto più velocemente di altre proprietà. Archiviazione Azure utilizza **PartitionKey** per distribuire automaticamente le entità della tabella su molti nodi di archiviazione. Le entità con lo stesso oggetto **PartitionKey** vengono archiviate nello stesso nodo. RowKey** è l'ID univoco dell'entità all'interno della partizione cui appartiene.**

	entity = { "content" => "test entity",
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.insert_entity("testtable", entity)

## Aggiornare un'entità

Esistono vari metodi per aggiornare un'entità esistente:

* **update\_entity():** consente di aggiornare un'entità esistente sostituendola.
* **merge\_entity():** consente di aggiornare un'entità esistente unendovi i nuovi valori di proprietà.
* **insert\_or\_merge\_entity():** aggiorna un'entità esistente sostituendola. Se non esiste alcuna entità, ne verrà inserita una nuova:
* **insert\_or\_replace\_entity():** consente di aggiornare un'entità esistente unendovi i nuovi valori di proprietà. Se non esiste alcuna entità, ne verrà inserita una nuova.

Nell'esempio seguente viene dimostrato l'aggiornamento di un'entità mediante l'utilizzo di **update\_entity**:

	entity = { "content" => "test entity with updated content",
	  :PartitionKey => "test-partition-key", :RowKey => "1" }
	azure_table_service.update_entity("testtable", entity)

Con **update\_entity()** e **merge\_entity()**, se l'entità che si sta aggiornando non esiste, l'operazione di aggiornamento non riuscirà. Se pertanto si desidera archiviare un'entità indipendentemente dal fatto che esista o meno, è necessario utilizzare **insert\_or\_replace\_entity()** oppure **insert\_or\_merge\_entity()**.

## Usare i gruppi di entità

È talvolta consigliabile inviare più operazioni in un batch per garantire l'elaborazione atomica da parte del server. A tale scopo, è necessario prima creare un oggetto **Batch** e quindi utilizzare il metodo **execute\_batch()** su **TableService**. Nell'esempio seguente viene dimostrato l'invio di due entità con RowKey 2 e 3 in un batch: Si noti che funziona solo per le entità con lo stesso oggetto PartitionKey.

	azure_table_service = Azure::TableService.new
	batch = Azure::Storage::Table::Batch.new("testtable",
	  "test-partition-key") do
	  insert "2", { "content" => "new content 2" }
	  insert "3", { "content" => "new content 3" }
	end
	results = azure_table_service.execute_batch(batch)

## Eseguire una query su un'entità

Per eseguire una query su un'entità in una tabella, utilizzare il metodo **get\_entity()** passando il nome tabella e i parametri **PartitionKey** e **RowKey**.

	result = azure_table_service.get_entity("testtable", "test-partition-key",
	  "1")

## Eseguire query su un set di entità

Per eseguire query su un set di entità in una tabella, creare un oggetto hash di query e utilizzare il metodo **query\_entities()**. Nell'esempio seguente viene dimostrato l'invio di tutte le entità con lo stesso oggetto **PartitionKey**:

	query = { :filter => "PartitionKey eq 'test-partition-key'" }
	result, token = azure_table_service.query_entities("testtable", query)

> [AZURE.NOTE] Se il risultato impostato è troppo grande affinché sia restituito da un'unica query, verrà restituito un token di continuazione per recuperare le pagine successive.

## Eseguire query su un subset di proprietà di entità

Mediante una query su una tabella è possibile recuperare solo alcune proprietà da un'entità. Questa tecnica, denominata "proiezione", consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. Usare la clausola select e passare i nomi delle proprietà da inoltrare al client.

	query = { :filter => "PartitionKey eq 'test-partition-key'",
	  :select => ["content"] }
	result, token = azure_table_service.query_entities("testtable", query)

## Eliminare un'entità

Per eliminare un'entità utilizzare il metodo **delete\_entity()**. È necessario passare il nome della tabella contenente l'entità e i parametri PartitionKey e RowKey dell'entità.

		azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## Eliminare una tabella

Per eliminare una tabella utilizzare il metodo **delete\_table()** e passare il nome della tabella che si desidera eliminare.

		azure_table_service.delete_table("testtable")

## Passaggi successivi

Seguire i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

- [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Archivio [Azure SDK per Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) su GitHub

<!---HONumber=AcomDC_0128_2016-->