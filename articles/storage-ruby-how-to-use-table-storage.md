<properties linkid="dev-ruby-how-to-table-services" urlDisplayName="Table Service" pageTitle="How to use table storage (Ruby) | Microsoft Azure" metaKeywords="Azure table storage service, Azure table service Ruby, table storage Ruby" description="Learn how to use the table storage service in Azure. Code samples are written using the Ruby API." metaCanonical="" services="storage" documentationCenter="Ruby" title="How to Use the Table Service from Ruby" authors="guayan" solutions="" manager="" editor="" />

Come utilizzare il Servizio tabelle da Ruby
==========================

In questa guida verranno illustrati diversi scenari comuni di utilizzo del servizio tabelle di Azure. Gli esempi sono scritti utilizzando l'API Ruby. Gli scenari presentati includono **creazione ed eliminazione di una tabella, inserimento di entità ed esecuzione di query sulle entità in una tabella**. Per ulteriori informazioni sulle tabelle, vedere la sezione [Passaggi successivi](#next-steps).

Sommario
----

-   [Informazioni sul Servizio tabelle](#what-is)
-   [Concetti](#concepts)
-   [Creazione di un account di archiviazione di Azure](#create-a-windows-azure-storage-account)
-   [Creazione di un'applicazione Ruby](#create-a-ruby-application)
-   [Configurazione dell'applicazione per l'accesso all'archiviazione](#configure-your-application-to-access-storage)
-   [Configurazione di una connessione di archiviazione di Azure](#setup-a-windows-azure-storage-connection)
-   [Procedura: Creare una tabella](#how-to-create-a-table)
-   [Procedura: Aggiungere un'entità a una tabella](#how-to-add-an-entity-to-a-table)
-   [Procedura: Aggiornare un'entità](#how-to-update-an-entity)
-   [Procedura: Utilizzare i gruppi di entità](#how-to-work-with-groups-of-entities)
-   [Procedura: Eseguire una query su un'entità](#how-to-query-for-an-entity)
-   [Procedura: Eseguire query su un set di entità](#how-to-query-a-set-of-entities)
-   [Procedura: Eseguire una query su un subset di proprietà di entità](#how-to-query-a-subset-of-entity-properties)
-   [Procedura: Eliminare un'entità](#how-to-delete-an-entity)
-   [Procedura: Eliminare una tabella](#how-to-delete-a-table)
-   [Passaggi successivi](#next-steps)

[WACOM.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

Creazione di un account di archiviazione di Azure
---------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Creazione di un'applicazione Ruby
------------------

Creare un'applicazione Ruby. Per istruzioni, vedere [Creazione di un'applicazione Ruby in Azure](/en-us/develop/ruby/tutorials/web-app-with-linux-vm/).

Configurazione dell'applicazione per l'accesso all'archiviazione
----------------------------

Per utilizzare l'archiviazione di Azure, è necessario scaricare e utilizzare il pacchetto Ruby Azure, che comprende un set di librerie che comunicano con i servizi di archiviazione REST.

### Utilizzare RubyGems per ottenere il pacchetto

1.  Utilizzare un'interfaccia della riga di comando come **PowerShell** (Windows,) **Terminale** (Mac) o **Bash** (Unix).

2.  Digitare **gem install azure** nella finestra di comando per installare la gemma e le dipendenze.

### Importare il pacchetto

Utilizzando l'editor di testo preferito aggiungere quanto segue alla parte superiore del file Ruby dove si intende utilizzare l'archiviazione:

    require "azure"

Configurazione di una connessione di archiviazione di Azure
---------------------

Il modulo di Azure leggerà le variabili di ambiente **AZURE\_STORAGE\_ACCOUNT** e **AZURE\_STORAGE\_ACCESS\_KEY** per ottenere le informazioni necessarie per la connessione all'account di archiviazione di Azure. Se queste variabili di ambiente non sono impostate, sarà necessario specificare le informazioni relative all'account prima di utilizzare **Azure::TableService** con il codice seguente:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

Per ottenere questi valori:

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/).

2.  Passare all'account di archiviazione che si desidera utilizzare.

3.  Fare clic su **MANAGE KEYS** nella parte inferiore del pannello di navigazione.

4.  Nella finestra di dialogo popup saranno visualizzati il nome dell'account di archiviazione, la chiave di accesso primaria e la chiave di accesso secondaria. Per la chiave di accesso è possibile scegliere la primaria o la secondaria.

Come creare una tabella
--------------

L'oggetto **Azure::TableService** consente di utilizzare le tabelle e le entità. Per creare una tabella utilizzare il metodo **create\_table()**. Nell'esempio seguente viene creata una tabella o stampato l'eventuale errore.

    azure_table_service = Azure::TableService.new
    begin
      azure_table_service.create_table("testtable")
    rescue
      puts $!
    end

Come aggiungere un'entità a una tabella
----------------------

Per aggiungere un'entità, creare innanzitutto un oggetto hash che definisca le proprietà dell'entità. Si noti che per ogni entità è necessario specificare un oggetto **PartitionKey** e un oggetto **RowKey**. Si tratta di identificatori univoci dell'entità e sono valori che possono essere interrogati molto più velocemente di altre proprietà. Il servizio di archiviazione Azure utilizza **PartitionKey** per distribuire automaticamente le entità della tabella su molti nodi di archiviazione. Le entità con lo stesso oggetto **PartitionKey** vengono archiviate nello stesso nodo. RowKey **è l'ID univoco dell'entità all'interno della partizione cui appartiene.**

    entity = { "content" => "test entity", 
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.insert_entity("testtable", entity)

Procedura: Aggiornare un'entità
--------------

Esistono vari metodi per aggiornare un'entità esistente:

-   **update\_entity():** consente di aggiornare un'entità esistente sostituendola.
-   **merge\_entity():** consente di aggiornare un'entità esistente unendovi i nuovi valori di proprietà.
-   **insert\_or\_merge\_entity():** consente di aggiornare un'entità esistente sostituendola. Se non esiste alcuna entità, ne verrà inserita una nuova:
-   **insert\_or\_replace\_entity():** consente di aggiornare un'entità esistente unendovi i nuovi valori di proprietà. Se non esiste alcuna entità, ne verrà inserita una nuova.

Nell'esempio seguente viene dimostrato l'aggiornamento di un'entità mediante l'utilizzo di **update\_entity**:

    entity = { "content" => "test entity with updated content", 
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.update_entity("testtable", entity)

Con **update\_entity()** e **merge\_entity()**, se l'entità da aggiornare non esiste, l'operazione di aggiornamento non riuscirà. Se pertanto si desidera archiviare un'entità indipendentemente dal fatto che esista o meno, è necessario utilizzare **insert\_or\_replace\_entity()** oppure **insert\_or\_merge\_entity()**.

Procedura: Utilizzare i gruppi di entità
------------------

È talvolta consigliabile inviare più operazioni in un batch per garantire l'elaborazione atomica da parte del server. A tale scopo, è necessario prima creare un oggetto **Batch** e quindi utilizzare il metodo **execute\_batch()** su **TableService**. Nell'esempio seguente viene dimostrato l'invio di due entità con RowKey 2 e 3 in un batch: Si noti che funziona solo per le entità con lo stesso oggetto PartitionKey.

    azure_table_service = Azure::TableService.new
    batch = Azure::Storage::Table::Batch.new("testtable", 
      "test-partition-key") do
      insert "2", { "content" => "new content 2" }
      insert "3", { "content" => "new content 3" }
    end
    results = azure_table_service.execute_batch(batch)

Procedura: Eseguire una query su un'entità
--------------

Per eseguire una query su un'entità in una tabella, utilizzare il metodo **get\_entity()** passando il nome tabella e i parametri **PartitionKey** e **RowKey**.

    result = azure_table_service.get_entity("testtable", "test-partition-key", 
      "1")

Procedura: Eseguire query su un set di entità
------------------

Per eseguire query su un set di entità in una tabella, creare un oggetto hash di query e utilizzare il metodo **query\_entities()**. Nell'esempio seguente viene dimostrato l'invio di tutte le entità con lo stesso oggetto **PartitionKey**:

    query = { :filter => "PartitionKey eq 'test-partition-key'" }
    result, token = azure_table_service.query_entities("testtable", query)

**Si noti** che se il risultato impostato è troppo grande affinché sia restituito da un'unica query, verrà restituito un token di continuazione per recuperare le pagine successive.

Procedura: Eseguire una query su un subset di proprietà di entità
------------------------

Mediante una query su una tabella è possibile recuperare solo alcune proprietà da un'entità. Questa tecnica, denominata "proiezione", consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. Utilizzare la clausola select e passare i nomi delle proprietà da inoltrare al client.

    query = { :filter => "PartitionKey eq 'test-partition-key'", 
      :select => ["content"] }
    result, token = azure_table_service.query_entities("testtable", query)

Procedura: Eliminare un'entità
--------------

Per eliminare un'entità utilizzare il metodo **delete\_entity()**. È necessario passare il nome della tabella contenente l'entità e i parametri PartitionKey e RowKey dell'entità.

     azure_table_service.delete_entity("testtable", "test-partition-key", "1")

Procedura: Eliminare una tabella
----------------

Per eliminare una tabella utilizzare il metodo **delete\_table()** e passare il nome della tabella che si desidera eliminare.

     azure_table_service.delete_table("testtable")

Passaggi successivi
--------

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione tabelle, visitare i collegamenti seguenti per ulteriori informazioni sulle attività di archiviazione più complesse.

-   Riferimento in MSDN: [Archiviazione e accesso ai dati in Azure](http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx)
-   [Blog del team di Archiviazione di Azure](http://blogs.msdn.com/b/windowsazurestorage/)
-   Archivio [Azure SDK for Ruby](http://github.com/WindowsAzure/azure-sdk-for-ruby) su GitHub

