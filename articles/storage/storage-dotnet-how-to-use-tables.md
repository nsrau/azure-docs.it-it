---
title: Introduzione all&quot;archiviazione tabelle di Azure con .NET | Documentazione Microsoft
description: Archiviare dati non strutturati nel cloud con il servizio di archiviazione tabelle di Azure, ovvero un archivio dati NoSQL.
services: storage
documentationcenter: .net
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: fe46d883-7bed-49dd-980e-5c71df36adb3
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 04/10/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 0764d4cbcd618be54c8b6e71a632d24c5c3bfe67
ms.lasthandoff: 04/10/2017


---
# <a name="get-started-with-azure-table-storage-using-net"></a>Introduzione all'archiviazione tabelle di Azure con .NET
[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-check-out-samples-dotnet](../../includes/storage-check-out-samples-dotnet.md)]

L'archiviazione tabelle di Azure è un servizio che archivia dati NoSQL strutturati nel cloud, mettendo a disposizione un archivio di chiavi/attributi senza schema. Poiché l'archiviazione tabelle è senza schema, è facile adattare i dati con il variare delle esigenze dell'applicazione. L'accesso ai dati dell'archiviazione tabelle è rapido ed economico per molti tipi di applicazioni e presenta costi generalmente più bassi rispetto alle soluzioni SQL tradizionali per volumi di dati simili.

È possibile usare l'archiviazione tabelle per archiviare set di dati flessibili, ad esempio i dati utente per le applicazioni Web, le rubriche, le informazioni sui dispositivi o altri tipi di metadati richiesti dal servizio. In una tabella possono essere archiviate il numero desiderato di tabelle e un account di archiviazione può contenere un numero qualsiasi di tabelle, fino a che non viene raggiunto il limite di capacità dell'account di archiviazione.

### <a name="about-this-tutorial"></a>Informazioni sull'esercitazione
Questa esercitazione illustra come usare la [libreria client di archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/) in alcuni scenari comuni dell'archiviazione tabelle di Azure. Gli scenari vengono presentati con esempi C# per la creazione ed eliminazione di una tabella e l'inserimento, l'aggiornamento, l'eliminazione e l'esecuzione di query per i dati delle tabelle.

## <a name="prerequisites"></a>Prerequisiti

Per completare l'esercitazione sono necessari gli elementi seguenti:

* [Microsoft Visual Studio](https://www.visualstudio.com/visual-studio-homepage-vs.aspx)
* [Libreria client di archiviazione di Azure per .NET](https://www.nuget.org/packages/WindowsAzure.Storage/)
* [Gestione configurazione di Azure per .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/)
* [Account di archiviazione di Azure](storage-create-storage-account.md#create-a-storage-account)

[!INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

### <a name="more-samples"></a>Altri esempi
Per altri esempi di uso dell'archivio tabelle, vedere [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)(Introduzione all'archivio tabelle di Azure in .NET). È possibile scaricare l'applicazione di esempio ed eseguirla oppure esaminare il codice in GitHub.

[!INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[!INCLUDE [storage-development-environment-include](../../includes/storage-development-environment-include.md)]

### <a name="add-using-directives"></a>Aggiungere le direttive using
Aggiungere le direttive **using** seguenti all'inizio del file `Program.cs`:

```csharp
using Microsoft.Azure; // Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage; // Namespace for CloudStorageAccount
using Microsoft.WindowsAzure.Storage.Table; // Namespace for Table storage types
```

### <a name="parse-the-connection-string"></a>Analizzare la stringa di connessione
[!INCLUDE [storage-cloud-configuration-manager-include](../../includes/storage-cloud-configuration-manager-include.md)]

### <a name="create-the-table-service-client"></a>Creare il client del servizio tabelle
La classe [CloudTableClient][dotnet_CloudTableClient] consente di recuperare le tabelle e le entità archiviate nell'archiviazione tabelle. Di seguito è illustrato un modo per creare il client del servizio tabelle:

```csharp
// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
```

A questo punto si è pronti a scrivere codice che legge e scrive i dati nell'archivio tabelle.

## <a name="create-a-table"></a>Creare una tabella
Questo esempio illustra come creare una tabella, se non esiste già:

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Retrieve a reference to the table.
CloudTable table = tableClient.GetTableReference("people");

// Create the table if it doesn't exist.
table.CreateIfNotExists();
```

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella
Per eseguire il mapping di entità a oggetti C# viene usata una classe personalizzata derivata da [TableEntity][dotnet_TableEntity]. Per aggiungere un'entità a una classe, creare una classe che definisca le proprietà dell'entità. Il codice seguente consente di definire una classe di entità che utilizza il nome e il cognome del cliente rispettivamente come chiave di riga e chiave di partizione. La partizione e la chiave di riga di un'entità consentono di identificare in modo univoco l'entità nella tabella. Le query su entità con la stessa chiave di partizione vengono eseguite più rapidamente di quelle con chiavi di partizione diverse, tuttavia l'uso di chiavi di partizione diverse assicura una maggiore scalabilità delle operazioni parallele. Le entità da archiviare nelle tabelle devono essere di un tipo supportato, ad esempio derivato dalla classe [TableEntity][dotnet_TableEntity]. Le proprietà dell'entità da archiviare in una tabella devono essere proprietà pubbliche del tipo e supportare sia l'ottenimento che l'impostazione di valori. Il tipo di entità *deve* inoltre esporre un costruttore senza parametri.

```csharp
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
```

Le operazioni su tabella che interessano le entità vengono eseguite tramite l'oggetto [CloudTable][dotnet_CloudTable] creato precedentemente nella sezione "Creare una tabella". L'operazione da eseguire è rappresentata da un oggetto [TableOperation][dotnet_TableOperation]. L'esempio di codice seguente illustra la creazione dell'oggetto [CloudTable][dotnet_CloudTable] e quindi di un oggetto **CustomerEntity**. Per preparare l'operazione, viene creato un oggetto [TableOperation][dotnet_TableOperation] per inserire l'entità customer nella tabella. L'operazione viene infine eseguita chiamando [CloudTable][dotnet_CloudTable].[Execute][dotnet_CloudTable_Execute].

```csharp
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

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
table.Execute(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Inserire un batch di entità
Per inserire un batch di entità in una tabella, è possibile usare un'unica operazione di scrittura. Di seguito sono riportate altre informazioni sulle operazioni batch:

* È possibile utilizzare una singola operazione batch per eseguire operazioni di aggiornamento, eliminazione e inserimento.
* Una singola operazione batch può includere fino a 100 entità.
* A tutte le entità di una singola operazione batch deve essere associata la stessa chiave di partizione.
* È possibile eseguire una query come operazione batch, ma deve essere l'unica operazione del batch.

L'esempio di codice seguente consente di creare due oggetti entità e di aggiungere ciascuno a un oggetto [TableBatchOperation][dotnet_TableBatchOperation] usando il metodo [Insert][dotnet_TableBatchOperation_Insert]. Per eseguire l'operazione viene quindi chiamato [CloudTable][dotnet_CloudTable].[ExecuteBatch][dotnet_CloudTable_ExecuteBatch].

```csharp
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
```

## <a name="retrieve-all-entities-in-a-partition"></a>Recuperare tutte le entità di una partizione
Per eseguire una query su una tabella e recuperare tutte le entità di una partizione, usare un oggetto [TableQuery][dotnet_TableQuery]. Nell'esempio di codice seguente viene specificato un filtro per le entità in cui la chiave di partizione è 'Smith'. Questo esempio consente di stampare sulla console i campi di ogni entità inclusa nei risultati della query.

```csharp
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
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>Recuperare un intervallo di entità in una partizione
Se non si vuole eseguire una query su tutte le entità di una partizione, è possibile specificare un intervallo combinando il filtro della chiave di partizione con quello della chiave di riga. L'esempio di codice seguente usa due filtri per recuperare tutte le entità della partizione 'Smith' in cui la chiave di riga (nome) inizia con una lettera che precede la 'E' nell'alfabeto. Stampa quindi i risultati della query.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
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
```

## <a name="retrieve-a-single-entity"></a>Recuperare una singola entità
Per recuperare una singola entità specifica, è possibile scrivere una query. Il codice seguente usa [TableOperation][dotnet_TableOperation] per specificare il cliente 'Ben Smith'. Questo metodo restituisce una sola entità anziché una raccolta e il valore restituito in [TableResult][dotnet_TableResult].[Result][dotnet_TableResult_Result] è un oggetto **CustomerEntity**. La specifica delle chiavi di partizione e di riga in una query costituisce la soluzione più rapida per recuperare una singola entità dal servizio tabelle.

```csharp
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
{
    Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
}
else
{
    Console.WriteLine("The phone number could not be retrieved.");
}
```

## <a name="replace-an-entity"></a>Sostituire un'entità
Per aggiornare un'entità, recuperarla dal servizio tabelle, modificare l'oggetto entità e quindi salvare le modifiche nel servizio tabelle. Il codice seguente consente di modificare il numero di telefono di un cliente esistente. Invece di una chiamata a [Insert][dotnet_TableOperation_Insert], nel codice viene usato [Replace][dotnet_TableOperation_Replace]. Con [Replace][dotnet_TableOperation_Replace], l'entità viene completamente sostituita nel server, a meno che non sia stata modificata dopo essere stata recuperata. In questo caso, infatti, l'operazione non viene eseguita per impedire all'applicazione di sovrascrivere inavvertitamente una modifica effettuata tra il recupero e l'aggiornamento da parte di un altro componente dell'applicazione. Per risolvere questo errore, recuperare di nuovo l'entità, apportare le modifiche, se ancora valide, quindi eseguire un'altra operazione [Replace][dotnet_TableOperation_Replace]. La sezione successiva illustra come ovviare a questo comportamento.

```csharp
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
    updateEntity.PhoneNumber = "425-555-0105";

    // Create the Replace TableOperation.
    TableOperation updateOperation = TableOperation.Replace(updateEntity);

    // Execute the operation.
    table.Execute(updateOperation);

    Console.WriteLine("Entity updated.");
}
else
{
    Console.WriteLine("Entity could not be retrieved.");
}
```

## <a name="insert-or-replace-an-entity"></a>Inserire o sostituire un'entità
Le operazioni [Replace][dotnet_TableOperation_Replace] non vengono eseguite se l'entità è stata modificata dopo essere stata recuperata dal server. Per la corretta esecuzione dell'operazione [Replace][dotnet_TableOperation_Replace] è anche necessario recuperare prima l'entità dal server. In alcuni casi, tuttavia, non è noto se l'entità è già esistente nel server e i valori in essa archiviati sono irrilevanti, pertanto devono essere sovrascritti completamente dall'aggiornamento. A tale scopo è necessario usare un'operazione [InsertOrReplace][dotnet_TableOperation_InsertOrReplace]. Questa operazione inserisce l'entità se non è già esistente oppure la sostituisce se esiste già, indipendentemente dalla data dell'ultimo aggiornamento.

Nell'esempio di codice seguente, viene creata e inserita nella tabella 'people' un'entità customer per 'Fred Jones'. Viene quindi usata l'operazione [InsertOrReplace][dotnet_TableOperation_InsertOrReplace] per salvare nel server un'entità con la stessa chiave di partizione (Jones) e chiave di riga (Fred), questa volta con un valore diverso per la proprietà PhoneNumber. Dato che si usa [InsertOrReplace][dotnet_TableOperation_InsertOrReplace], tutti i valori delle proprietà vengono sostituiti. Se tuttavia un'entità 'Fred Jones' non fosse già esistita nella tabella, sarebbe stata inserita.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Create a customer entity.
CustomerEntity customer3 = new CustomerEntity("Jones", "Fred");
customer3.Email = "Fred@contoso.com";
customer3.PhoneNumber = "425-555-0106";

// Create the TableOperation object that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer3);

// Execute the operation.
table.Execute(insertOperation);

// Create another customer entity with the same partition key and row key.
// We've already created a 'Fred Jones' entity and saved it to the
// 'people' table, but here we're specifying a different value for the
// PhoneNumber property.
CustomerEntity customer4 = new CustomerEntity("Jones", "Fred");
customer4.Email = "Fred@contoso.com";
customer4.PhoneNumber = "425-555-0107";

// Create the InsertOrReplace TableOperation.
TableOperation insertOrReplaceOperation = TableOperation.InsertOrReplace(customer4);

// Execute the operation. Because a 'Fred Jones' entity already exists in the
// 'people' table, its property values will be overwritten by those in this
// CustomerEntity. If 'Fred Jones' didn't already exist, the entity would be
// added to the table.
table.Execute(insertOrReplaceOperation);
```

## <a name="query-a-subset-of-entity-properties"></a>Eseguire query su un subset di proprietà di entità
Una query tabella consente di recuperare alcune proprietà da un'entità, ma non tutte. Questa tecnica, denominata proiezione, consente di ridurre la larghezza di banda e di migliorare le prestazioni della query, in particolare per entità di grandi dimensioni. La query nel codice seguente restituisce solo gli indirizzi di posta elettronica di entità nella tabella. A tale scopo viene usata una query di [DynamicTableEntity][dotnet_DynamicTableEntity] e anche [EntityResolver][dotnet_EntityResolver]. Per altre informazioni sulla proiezione, vedere il post di blog [Introducing Upsert and Query Projection][blog_post_upsert] (Introduzione di Upsert e proiezione di query). La proiezione non è supportata dall'emulatore di archiviazione, quindi questo codice viene eseguito solo se si usa un account nel servizio tabelle.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Define the query, and select only the Email property.
TableQuery<DynamicTableEntity> projectionQuery = new TableQuery<DynamicTableEntity>().Select(new string[] { "Email" });

// Define an entity resolver to work with the entity after retrieval.
EntityResolver<string> resolver = (pk, rk, ts, props, etag) => props.ContainsKey("Email") ? props["Email"].StringValue : null;

foreach (string projectedEmail in table.ExecuteQuery(projectionQuery, resolver, null, null))
{
    Console.WriteLine(projectedEmail);
}
```

## <a name="delete-an-entity"></a>Eliminare un'entità
Per eliminare facilmente un'entità dopo averla recuperata, è possibile usare lo stesso modello illustrato per aggiornare un'entità. Il codice seguente consente di recuperare ed eliminare un'entità customer.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
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
{
    Console.WriteLine("Could not retrieve the entity.");
}
```

## <a name="delete-a-table"></a>Eliminare una tabella
L'esempio di codice seguente consente infine di eliminare una tabella dall'account di archiviazione. Una tabella eliminata non potrà essere creata nuovamente per un certo periodo di tempo.

```csharp
// Retrieve the storage account from the connection string.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable that represents the "people" table.
CloudTable table = tableClient.GetTableReference("people");

// Delete the table it if exists.
table.DeleteIfExists();
```

## <a name="retrieve-entities-in-pages-asynchronously"></a>Recuperare entità nelle pagine in modo asincrono
Se si legge un numero elevato di entità e si desidera elaborare/visualizzare le entità man mano che vengono recuperate anziché attendere che vengano tutte restituite, è possibile recuperare le entità utilizzando una query segmentata. In questo esempio viene spiegato come restituire i risultati nelle pagine utilizzando il modello Async-Await, in modo che l'esecuzione non venga bloccata durante l'attesa della restituzione di un set di risultati di grandi dimensioni. Per ulteriori informazioni sull'utilizzo del modello Async-Await in .NET, vedere [Programmazione asincrona con Async e Await (C# e Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx).

```csharp
// Initialize a default TableQuery to retrieve all the entities in the table.
TableQuery<CustomerEntity> tableQuery = new TableQuery<CustomerEntity>();

// Initialize the continuation token to null to start from the beginning of the table.
TableContinuationToken continuationToken = null;

do
{
    // Retrieve a segment (up to 1,000 entities).
    TableQuerySegment<CustomerEntity> tableQueryResult =
        await table.ExecuteQuerySegmentedAsync(tableQuery, continuationToken);

    // Assign the new continuation token to tell the service where to
    // continue on the next iteration (or null if it has reached the end).
    continuationToken = tableQueryResult.ContinuationToken;

    // Print the number of rows retrieved.
    Console.WriteLine("Rows retrieved {0}", tableQueryResult.Results.Count);

// Loop until a null continuation token is received, indicating the end of the table.
} while(continuationToken != null);
```

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base dell'archiviazione tabelle, visitare i collegamenti seguenti per altre informazioni sulle attività di archiviazione più complesse:

* Per altri esempi di archivio tabelle, vedere [Getting Started with Azure Table Storage in .NET](https://azure.microsoft.com/documentation/samples/storage-table-dotnet-getting-started/)
* Per informazioni dettagliate sulle API disponibili, vedere la documentazione di riferimento del servizio tabelle:
  * [Informazioni di riferimento sulla libreria client di archiviazione per .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
  * [Informazioni di riferimento sulle API REST](http://msdn.microsoft.com/library/azure/dd179355)
* Per altre informazioni su come semplificare il codice scritto da usare con Archiviazione di Azure, vedere [Informazioni su Azure WebJobs SDK](../app-service-web/websites-dotnet-webjobs-sdk-get-started.md)
* Per ulteriori opzioni di archiviazione dei dati in Azure, consultare altre guide alle funzionalità.
  * [Introduzione all'archivio BLOB di Azure con .NET](storage-dotnet-how-to-use-blobs.md) .
  * Per archiviare i dati relazionali, vedere [Connettersi al database SQL tramite .NET (C#)](../sql-database/sql-database-develop-dotnet-simple.md).

[Download and install the Azure SDK for .NET]: /develop/net/
[Creating an Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx

[blog_post_upsert]: http://blogs.msdn.com/b/windowsazurestorage/archive/2011/09/15/windows-azure-tables-introducing-upsert-and-query-projection.aspx

[dotnet_api_ref]: https://msdn.microsoft.com/library/azure/mt347887.aspx
[dotnet_CloudTableClient]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtableclient.aspx
[dotnet_CloudTable]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.aspx
[dotnet_CloudTable_Execute]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.execute.aspx
[dotnet_CloudTable_ExecuteBatch]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.cloudtable.executebatch.aspx
[dotnet_DynamicTableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.dynamictableentity.aspx
[dotnet_EntityResolver]: https://msdn.microsoft.com/library/jj733144.aspx
[dotnet_TableBatchOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.aspx
[dotnet_TableBatchOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablebatchoperation.insert.aspx
[dotnet_TableEntity]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableentity.aspx
[dotnet_TableOperation]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.aspx
[dotnet_TableOperation_Insert]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insert.aspx
[dotnet_TableOperation_InsertOrReplace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.insertorreplace.aspx
[dotnet_TableOperation_Replace]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableoperation.replace.aspx
[dotnet_TableQuery]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tablequery.aspx
[dotnet_TableResult]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.aspx
[dotnet_TableResult_Result]: https://msdn.microsoft.com/library/microsoft.windowsazure.storage.table.tableresult.result.aspx

