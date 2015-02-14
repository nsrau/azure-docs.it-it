<properties 
	pageTitle="Come usare l'archiviazione tabelle da .NET | Microsoft Azure" 
	description="Informazioni su come usare il servizio di archiviazione tabelle di Microsoft Azure per creare ed eliminare tabelle e per inserire ed eseguire query su entità di una tabella." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/10/2014" 
	ms.author="tamram"/>


# Come usare l'archiviazione tabelle da .NET


Questa guida illustra diversi scenari d'uso comuni del 
Servizio di archiviazione tabelle di Azure. Negli esempi, scritti in codice C\#, viene usata la libreria del client di archiviazione di Azure per .NET. Gli scenari presentati includono **creazione ed eliminazione di una tabella** e **uso di entità di tabella**. Per altre informazioni sulle tabelle, vedere la sezione [Passaggi successivi][].

> [AZURE.NOTE] Questa guida fa riferimento alla libreria client di archiviazione di Azure per .NET 2.x e versioni successive. La versione consigliata è la libreria client di archiviazione 4.x, disponibile tramite [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) o come parte di [Azure SDK per .NET](/it-it/downloads/). Vedere [Procedura: Accedere all'archiviazione tabelle a livello di codice][], per informazioni su come ottenere la libreria client di archiviazione.

## Sommario

-   [Informazioni sul servizio tabelle][]
-   [Concetti][]
-   [Creare un account di archiviazione di Azure][]
-   [Configurare una stringa di connessione di archiviazione][]
-   [Procedura: Accedere all'archiviazione tabelle a livello di codice][]
-   [Procedura: Creare una tabella][]
-   [Procedura: Aggiungere un'entità a una tabella][]
-   [Procedura: Inserire un batch di entità][]
-   [Procedura: Recuperare tutte le entità di una partizione][]
-   [Procedura: Recuperare un intervallo di entità in una partizione][]
-   [Procedura: Recuperare una singola entità][]
-   [Procedura: Sostituire un'entità][]
-   [Procedura: Inserire o sostituire un'entità][]
-   [Procedura: Eseguire query su un subset di proprietà di entità][]
-   [Procedura: Eliminare un'entità][]
-   [Procedura: Eliminare una tabella][]
-   [Passaggi successivi][]

[AZURE.INCLUDE [howto-table-storage](../includes/howto-table-storage.md)]

[AZURE.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

[AZURE.INCLUDE [storage-configure-connection-string](../includes/storage-configure-connection-string.md)]

## <h2> <a name="configure-access"> </a>Procedura: Accedere all'archiviazione tabelle a livello di codice</h2>

<h3>Recupero dell'assembly</h3>
Per ottenere l'assembly `Microsoft.WindowsAzure.Storage.dll`, è possibile usare NuGet. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.  Cercare online "WindowsAzure.Storage" e fare clic su **Installa** per installare il pacchetto Archiviazione di Azure e le dipendenze.

Il file `Microsoft.WindowsAzure.Storage.dll` è inoltre incluso in Azure SDK per .NET, che può essere scaricato dal <a href="http://www.windowsazure.com/it-it/develop/net/#">Centro per sviluppatori .NET</a>. L'assembly viene installato nella directory `%Program Files%\Microsoft SDKs\Windows Azure\.NET SDK\<sdk-version>\ref\`.

<h3>Dichiarazioni dello spazio dei nomi</h3>
Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere ad Archiviazione di Azure a livello di codice:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Table;

Assicurarsi di fare riferimento all'assembly `Microsoft.WindowsAzure.Storage.dll`.

<h3>Recupero della stringa di connessione</h3>
Per rappresentare le informazioni dell'account di archiviazione, è possibile usare il tipo **CloudStorageAccount**. Se si intende usare un modello di progetto di Azure e/o si dispone di un riferimento allo spazio dei nomi Microsoft.WindowsAzure.CloudConfigurationManager, è possibile usare il tipo **CloudConfigurationManager** per recuperare la stringa di connessione di archiviazione e le informazioni dell'account di archiviazione dalla configurazione dei servizi di Azure:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Se si intende creare un'applicazione senza riferimenti a Microsoft.WindowsAzure.CloudConfigurationManager e la stringa di connessione si trova nel file `web.config` o `app.config` come illustrato in precedenza, per recuperarla è possibile usare **ConfigurationManager**.  Sarà necessario aggiungere un riferimento a System.Configuration.dll al progetto e aggiungere un'altra dichiarazione dello spazio dei nomi:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

<h3>Dipendenze ODataLib</h3>
Le dipendenze ODataLib nella libreria client di archiviazione per .NET vengono risolte con i pacchetti ODataLib (versione 5.0.2) disponibili tramite NuGet e non WCF Data Services. È possibile scaricare le librerie ODataLib direttamente oppure farvi riferimento nel progetto del codice tramite NuGet. I pacchetti ODataLib specifici sono [OData], [Edm] e [Spatial].

<h2><a name="create-table"></a>Procedura: Creare una tabella</h2>

Per ottenere oggetti di riferimento per tabelle ed entità, è possibile usare un oggetto **CloudTableClient**. Il codice seguente consente di creare un oggetto **CloudTableClient** e di usarlo per creare una nuova tabella. In tutto il codice incluso in questa guida si presuppone che l'applicazione da compilare sia un progetto di Servizi cloud di Azure e che usi una stringa di connessione di archiviazione archiviata nella configurazione dei servizi dell'applicazione Azure.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the table if it doesn't exist.
    CloudTable table = tableClient.GetTableReference("people");
    table.CreateIfNotExists();

<h2><a name="add-entity"></a>Procedura: Aggiungere un'entità a una tabella</h2>

Per eseguire il mapping di entità a oggetti C\#, viene usata una classe personalizzata derivata da **TableEntity**. Per aggiungere un'entità a una classe, creare una classe che definisca le proprietà dell'entità. Il codice seguente consente di definire una classe di entità che usa il nome e il cognome del cliente rispettivamente come chiave di riga e chiave di partizione. La combinazione della chiave di riga e della chiave di partizione di un'entità consentono di identificare in modo univoco l'entità nella tabella. Le query su entità con la stessa chiave di partizione vengono eseguite più rapidamente di quelle con chiavi di partizione diverse, tuttavia l'uso di chiavi di partizione diverse assicura una maggiore scalabilità in caso di operazioni parallele. Tutte le proprietà da archiviare nel servizio tabelle devono essere di un tipo supportato che espone `get` e `set`.
Il tipo di entità *must* inoltre esporre un costruttore senza parametri.

    public class CustomerEntity : TableEntity
    {
        public CustomerEntity(string lastName, string firstName)
        {
            this.PartitionKey = lastName;
            this.RowKey = firstName;
        }
		
        public CustomerEntity() { }

        public string Email { get; set; }

        public string PhoneNumber { get; set; }
    }

Per eseguire le operazioni su tabelle che interessano entità, viene usato l'oggetto **CloudTable** creato in "Procedura: Creare una tabella". L'operazione da eseguire è rappresentata da un oggetto **TableOperation**. Nell'esempio di codice seguente viene illustrata la creazione dell'oggetto **CloudTable** e quindi di un oggetto **CustomerEntity**.  Per preparare l'operazione, viene creato un oggetto **TableOperation** per inserire l'entità customer nella tabella.  Infine, per eseguire l'operazione viene chiamato **CloudTable.Execute**.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
       CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
	
	// Create the CloudTable object that represents the "people" table.
	CloudTable table = tableClient.GetTableReference("people");

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    table.Execute(insertOperation);

<h2><a name="insert-batch"></a>Procedura: Inserire un batch di entità</h2>

Per inserire un batch di entità in una tabella, è possibile usare un'unica operazione di scrittura. Di seguito sono riportate altre informazioni sulle operazioni batch:

1.  È possibile usare una singola operazione batch per eseguire operazioni di aggiornamento, eliminazione e inserimento.
2.  Una singola operazione batch può includere fino a 100 entità.
3.  A tutte le entità di una singola operazione batch deve essere associata la stessa chiave di partizione.
4.  È possibile eseguire una query come operazione batch, ma deve essere l'unica operazione del batch.

<!-- -->
L'esempio di codice seguente consente di creare due oggetti entità e di aggiungere ciascuno a un oggetto **TableBatchOperation** usando il metodo **Insert**. Per eseguire l'operazione, viene quindi chiamato **CloudTable.Execute**.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    
	// Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create the batch operation.
    TableBatchOperation batchOperation = new TableBatchOperation();

    // Create a customer entity and add it to the table.
	CustomerEntity customer1 = new CustomerEntity("Smith", "Jeff");
	customer1.Email = "Jeff@contoso.com";
	customer1.PhoneNumber = "425-555-0104";
            
	// Create another customer entity and add it to the table.
	CustomerEntity customer2 = new CustomerEntity("Smith", "Ben");
	customer2.Email = "Ben@contoso.com";
	customer2.PhoneNumber = "425-555-0102";
            
	// Add both customer entities to the batch insert operation.
	batchOperation.Insert(customer1);
	batchOperation.Insert(customer2);

	// Execute the batch operation.
	table.ExecuteBatch(batchOperation);

<h2><a name="retrieve-all-entities"></a>Procedura: Recuperare tutte le entità di una partizione</h2>

Per eseguire una query su una tabella e recuperare tutte le entità di una partizione, usare un oggetto **TableQuery**.
Nell'esempio di codice seguente viene specificato un filtro per le entità in cui la chiave di partizione è 'Smith'. Questo esempio consente di stampare sulla console i campi di ogni entità inclusa nei risultati della query.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    foreach (CustomerEntity entity in table.ExecuteQuery(query))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-range-entities"></a>Procedura: Recuperare un intervallo di entità in una partizione</h2>

Se non si desidera eseguire una query su tutte le entità di una partizione, è possibile specificare un intervallo combinando il filtro della chiave di partizione con quello della chiave di riga. Nell'esempio di codice seguente vengono usati due filtri per recuperare tutte le entità della partizione 'Smith' in cui la chiave di riga (nome) inizia con una lettera che precede la 'E' nell'alfabeto e quindi stampare i risultati della query.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

	// Create the table query.
    TableQuery<CustomerEntity> rangeQuery = new TableQuery<CustomerEntity>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, "E")));

    // Loop through the results, displaying information about the entity.
    foreach (CustomerEntity entity in table.ExecuteQuery(rangeQuery))
    {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
    }

<h2><a name="retrieve-single-entity"></a>Procedura: Recuperare una singola entità</h2>

Per recuperare una singola entità specifica, è possibile scrivere una query. Nel codice seguente viene usato un oggetto **TableOperation** per specificare il cliente 'Ben Smith'.
Questo metodo restituisce una sola entità, anziché una raccolta, e il valore restituito in **TableResult.Result** è un oggetto **CustomerEntity**.
La specifica delle chiavi di partizione e di riga in una query costituisce la soluzione più rapida per recuperare una singola entità dal servizio tabelle.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

<h2><a name="replace-entity"></a>Procedura: Sostituire un'entità</h2>

Per aggiornare un'entità, recuperarla dal servizio tabelle, modificare l'oggetto entità e quindi salvare le modifiche nel servizio tabelle. Il codice seguente consente di modificare il numero di telefono di un cliente esistente. Invece di una chiamata a **Insert**, nel codice viene usata l'operazione 
**Replace**. In questo modo l'entità viene completamente sostituita nel server, a meno che non sia stata modificata da quando è stata recuperata. In questo caso, infatti, l'operazione non viene eseguita  per impedire all'applicazione di sovrascrivere inavvertitamente una modifica effettuata tra il recupero e l'aggiornamento da parte di un altro componente dell'applicazione. Per risolvere questo errore, recuperare di nuovo l'entità, apportare le modifiche, se ancora valide, quindi eseguire un'altra operazione **Replace**.  Nella sezione successiva verrà illustrato come ovviare a questo comportamento.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-0105";

	   // Create the InsertOrReplace TableOperation
	   TableOperation updateOperation = TableOperation.Replace(updateEntity);

	   // Execute the operation.
	   table.Execute(updateOperation);

	   Console.WriteLine("Entity updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

<h2><a name="insert-or-replace-entity"></a>Procedura: Inserire o sostituire un'entità</h2>

Le operazioni **Replace** non vengono eseguite se l'entità è stata modificata rispetto a quando è stata recuperata dal server.  Per la corretta esecuzione di **Replace** è inoltre necessario recuperare prima l'entità dal server.
In alcuni casi, tuttavia, non è noto se l'entità è già esistente nel server e i valori in essa archiviati sono irrilevanti, pertanto devono essere sovrascritti completamente dall'aggiornamento.  A tale scopo, è necessario usare un'operazione **InsertOrReplace**.  Questa operazione inserisce l'entità se non è già esistente oppure la sostituisce se esiste già, indipendentemente dalla data dell'ultimo aggiornamento.  Nell'esempio di codice seguente l'entità customer per Ben Smith viene comunque recuperata, ma viene salvata di nuovo nel server tramite l'operazione **InsertOrReplace**.  Tutte le modifiche apportate all'entità tra le operazioni di recupero e aggiornamento verranno sovrascritte.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    // Create the CloudTable object that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity updateEntity = (CustomerEntity)retrievedResult.Result;

    if (updateEntity != null)
	{
	   // Change the phone number.
	   updateEntity.PhoneNumber = "425-555-1234";

	   // Create the InsertOrReplace TableOperation
	   TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(updateEntity);

	   // Execute the operation.
	   table.Execute(insertOrReplaceOperation);

	   Console.WriteLine("Entity was updated.");
	}

	else
	   Console.WriteLine("Entity could not be retrieved.");

<h2><a name="query-entity-properties"></a>Procedura: Eseguire query su un subset di proprietà di entità</h2>

Una query tabella consente di recuperare alcune proprietà da un'entità, ma non tutte. Questa tecnica, denominata proiezione, consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. La query nel codice seguente restituisce solo gli indirizzi di posta elettronica di entità nella tabella. A tale scopo viene usata una query di **DynamicTableEntity** e anche un oggetto **EntityResolver**. Per altre informazioni sulla proiezione, vedere questo [post di blog][]. Si noti che la proiezione non è supportata nell'emulatore di archiviazione locale, pertanto questo codice viene eseguito solo se si usa un account sul servizio tabelle.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Define the query, and only select the Email property
    TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

    // Define an entity resolver to work with the entity after retrieval.
    EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

    foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
    {
        Console.WriteLine(projectedEmail);
    }

<h2><a name="delete-entity"></a>Procedura: Eliminare un'entità</h2>

Per eliminare facilmente un'entità dopo averla recuperata, è possibile usare lo stesso modello illustrato per aggiornare un'entità.  Il codice seguente consente di recuperare ed eliminare un'entità customer.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = table.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation.
	if (deleteEntity != null)
	{
	   TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

	   // Execute the operation.
	   table.Execute(deleteOperation);

	   Console.WriteLine("Entity deleted.");
	}

	else
	   Console.WriteLine("Could not retrieve the entity.");

<h2><a name="delete-table"></a>Procedura: Eliminare una tabella</h2>

L'esempio di codice seguente consente infine di eliminare una tabella dall'account di archiviazione. Una tabella eliminata non potrà essere creata nuovamente per un certo periodo di tempo.

    // Retrieve the storage account from the connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

    //Create the CloudTable that represents the "people" table.
    CloudTable table = tableClient.GetTableReference("people");

    // Delete the table it if exists.
    table.DeleteIfExists();

<h2><a name="next-steps"></a>Passaggi successivi</h2>

A questo punto, dopo aver appreso le nozioni di base dell'archiviazione tabelle, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse.

<ul>
<li>Per informazioni dettagliate sulle API disponibili, vedere la documentazione di riferimento del servizio tabelle:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Informazioni di riferimento sulla libreria client di archiviazione per .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/it-it/library/windowsazure/dd179355">Informazioni di riferimento sulle API REST dei servizi di archiviazione</a></li>
  </ul>
</li>
<li>Per altre informazioni sulle attività avanzate che è possibile eseguire con Archiviazione di Azure, vedere <a href="http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx">Archiviazione</a>.</li>
<li>Per altre informazioni su come semplificare il codice scritto per usare Archiviazione di Azure, vedere <a href="../websites-dotnet-webjobs-sdk/">Introduzione a Azure WebJobs SDK.</li>
<li>Per altre opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.
  <ul>
    <li>Per archiviare dati non strutturati, usare <a href="/it-it/documentation/articles/storage-dotnet-how-to-use-blobs/">Archiviazione BLOB</a>.</li>
    <li>Per archiviare dati strutturati, usare <a href="/it-it/documentation/articles/storage-dotnet-how-to-use-queues/">Archiviazione di accodamento</a>.</li>
    <li>Per archiviare dati relazionali, usare <a href="/it-it/documentation/articles/sql-database-dotnet-how-to-use/">Database SQL</a>.</li>
  </ul>
</li>
</ul>

  [Passaggi successivi]: #next-steps
  [Informazioni sul servizio tabelle]: #what-is
  [Concetti]: #concepts
  [Creare un account di archiviazione di Azure]: #create-account
  [Creare un progetto Azure in Visual Studio]: #create-project
  [Configurare l'applicazione per l'accesso all'archiviazione]: #configure-access
  [Configurare una stringa di connessione di archiviazione]: #setup-connection-string
  [Procedura: Accedere all'archiviazione tabelle a livello di codice]: #configure-access
  [Procedura: Creare una tabella]: #create-table
  [Procedura: Aggiungere un'entità a una tabella]: #add-entity
  [Procedura: Inserire un batch di entità]: #insert-batch
  [Procedura: Recuperare tutte le entità di una partizione]: #retrieve-all-entities
  [Procedura: Recuperare un intervallo di entità in una partizione]: #retrieve-range-entities
  [Procedura: Recuperare una singola entità]: #retrieve-single-entity
  [Procedura: Sostituire un'entità]: #replace-entity
  [Procedura: Inserire o sostituire un'entità]: #insert-or-replace-entity
  [Procedura: Eseguire query su un subset di proprietà di entità]: #query-entity-properties
  [Procedura: Eliminare un'entità]: #delete-entity
  [Procedura: Eliminare una tabella]: #delete-table
  [Scaricare e installare Azure SDK per .NET]: /it-it/develop/net/
  [Creazione di un progetto Azure in Visual Studio]: http://msdn.microsoft.com/it-it/library/windowsazure/ee405487.aspx
  
  [Blob5]: ./media/storage-dotnet-how-to-use-table-storage/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-table-storage/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-table-storage/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-table-storage/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-table-storage/blob9.png
  
  [post di blog]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx
  [Informazioni di riferimento sulla libreria client .NET]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Archiviazione]: http://msdn.microsoft.com/it-it/library/windowsazure/gg433040.aspx
  [Blog del team di Archiviazione di Azure]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configurazione delle stringhe di connessione]: http://msdn.microsoft.com/it-it/library/windowsazure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2
<!--HONumber=42-->
