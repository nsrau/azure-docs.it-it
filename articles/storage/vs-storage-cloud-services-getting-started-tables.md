---
title: Introduzione all&quot;archiviazione tabelle e ai Servizi connessi di Visual Studio (servizi cloud) | Documentazione Microsoft
description: Informazioni su come iniziare a usare il servizio di archiviazione di tabella in un progetto di servizio di cloud in Visual Studio dopo aver eseguito la connessione a un account di archiviazione con i servizi connessi di Visual Studio.
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b4a270402222e75c8af2f2c3cf81b5ba8f1acad3


---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Introduzione all’archiviazione di tabella di Azure e ai servizi connessi di Visual Studio (progetti servizi cloud)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Panoramica
In questo articolo viene descritto come iniziare a utilizzare l'archiviazione tabelle di Azure in Visual Studio dopo aver creato o fatto riferimento a un account di archiviazione di Azure in un progetto servizi cloud usando la finestra di dialogo **Aggiungi servizi connessi** di Visual Studio. L'operazione **Aggiungi servizi connessi** consente di installare i pacchetti NuGet appropriati per accedere all'archiviazione di Azure nel progetto e di aggiungere la stringa di connessione per l'account di archiviazione ai file di configurazione del progetto.

Il servizio di archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un datastore NoSQL che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali.

Per iniziare, è innanzitutto necessario creare una tabella nell'account di archiviazione. Infine verrà mostrato come eseguire operazioni relative alle tabelle e all'entità di base, come l'aggiunta, la modifica, la lettura e la lettura delle entità delle tabelle. Negli esempi, scritti in codice C\#, viene usata la [libreria client di Archiviazione di Microsoft Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx).

**NOTA:** alcune API che eseguono chiamate ad Archiviazione di Azure sono asincrone. Per altre informazioni, vedere [Programmazione asincrona con Async e Await](http://msdn.microsoft.com/library/hh191443.aspx) . Nel codice riportato di seguito si presuppone vengano utilizzati i metodi di programmazione asincrona.

* Per altre informazioni sulla modifica delle tabelle a livello di codice, vedere [Introduzione all'archiviazione tabelle di Azure con .NET](storage-dotnet-how-to-use-tables.md) .
* Vedere la [documentazione di archiviazione](https://azure.microsoft.com/documentation/services/storage/) per informazioni generali sull'archiviazione di Azure.
* Vedere la [documentazione dei servizi Cloud](https://azure.microsoft.com/documentation/services/cloud-services/) per informazioni generali sui servizi cloud di Azure.
* Vedere [ASP.NET](http://www.asp.net) per ulteriori informazioni sulle applicazioni di programmazione di ASP.NET.

## <a name="access-tables-in-code"></a>Accesso alle tabelle nel codice
Per accedere alle tabelle nei progetti di servizio cloud, è necessario includere gli elementi seguenti ai file di origine C# che consentono di accedere all'archiviazione delle tabelle di Azure.

1. Assicurarsi che le dichiarazioni dello spazio dei nomi all'inizio del file C# includano queste istruzioni **using** .
   
        using Microsoft.Framework.Configuration;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Table;
        using System.Threading.Tasks;
        using LogLevel = Microsoft.Framework.Logging.LogLevel;
2. Ottenere un oggetto **CloudStorageAccount** che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure.
   
         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage account name>
         _AzureStorageConnectionString"));
   > [!NOTE]
   > Utilizzare tutto il codice riportato in precedenza prima del codice indicato negli esempi seguenti.
   > 
   > 
3. Ottenere un oggetto **CloudTableClient** per fare riferimento agli oggetti delle tabelle nell'account di archiviazione.
   
         // Create the table client.
         CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
4. Ottenere un oggetto di riferimento **CloudTable** per fare riferimento a tabelle ed entità specifiche.
   
        // Get a reference to a table named "peopleTable".
        CloudTable peopleTable = tableClient.GetTableReference("peopleTable");

## <a name="create-a-table-in-code"></a>Creazione di una tabella in codice
Per creare la tabella di Azure, è sufficiente aggiungere una chiamata a **CreateIfNotExistsAsync** per ottenere poi un oggetto **CloudTable** come descritto nella sezione "Accesso alle tabelle nel codice".

    // Create the CloudTable if it does not exist.
    await peopleTable.CreateIfNotExistsAsync();

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella
Per aggiungere un'entità a una classe, creare una classe che definisca le proprietà dell'entità. Il codice seguente permette di definire una classe di entità denominata **CustomerEntity** che usa il nome e il cognome del cliente rispettivamente come chiave di riga e chiave di partizione.

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

Per eseguire le operazioni su tabelle che interessano entità viene utilizzato l'oggetto **CloudTable** creato in precedenza in "Accesso alle tabelle nel codice". L'oggetto **TableOperation** rappresenta l'operazione da eseguire. L'esempio di codice seguente mostra come creare un oggetto **CloudTable** e un oggetto **CustomerEntity**. Per preparare l'operazione, viene creato un oggetto **TableOperation** per inserire l'entità customer nella tabella. Infine, per eseguire l'operazione viene chiamato **CloudTable.ExecuteAsync**.

    // Create a new customer entity.
    CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
    customer1.Email = "Walter@contoso.com";
    customer1.PhoneNumber = "425-555-0101";

    // Create the TableOperation that inserts the customer entity.
    TableOperation insertOperation = TableOperation.Insert(customer1);

    // Execute the insert operation.
    await peopleTable.ExecuteAsync(insertOperation);


## <a name="insert-a-batch-of-entities"></a>Inserire un batch di entità
È possibile inserire più entità in una tabella in una singola operazione di scrittura. L'esempio di codice seguente crea due oggetti entità ("Jeff Smith" e "Ben Smith"), li aggiunge a un oggetto **TableBatchOperation** usando il metodo Insert, quindi avvia l'operazione richiamando **CloudTable.ExecuteBatchAsync**.

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
    await peopleTable.ExecuteBatchAsync(batchOperation);

## <a name="get-all-of-the-entities-in-a-partition"></a>Ottenere tutte le entità di una partizione
Per eseguire una query su una tabella e recuperare tutte le entità di una partizione, usare un oggetto **TableQuery** . Nell'esempio di codice seguente viene specificato un filtro per le entità in cui la chiave di partizione è 'Smith'. Questo esempio consente di stampare sulla console i campi di ogni entità inclusa nei risultati della query.

    // Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
        .Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
        TableQuerySegment<CustomerEntity> resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
            Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
            entity.Email, entity.PhoneNumber);
        }
    } while (token != null);

    return View();


## <a name="get-a-single-entity"></a>Ottenere una singola entità
È possibile scrivere una query per ottenere una singola entità specifica. Il codice seguente usa un oggetto **TableOperation** per specificare un cliente denominato 'Ben Smith'. Questo metodo restituisce solo un'entità, anziché una raccolta, e il valore restituito in **TableResult.Result** è un oggetto **CustomerEntity**. La specifica delle chiavi di partizione e di riga in una query costituisce la soluzione più rapida per recuperare una singola entità dal servizio **tabelle** .

    // Create a retrieve operation that takes a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the retrieve operation.
    TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

    // Print the phone number of the result.
    if (retrievedResult.Result != null)
       Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
    else
       Console.WriteLine("The phone number could not be retrieved.");

## <a name="delete-an-entity"></a>Eliminare un'entità
È possibile eliminare un'entità dopo averla individuata. Il codice seguente cerca un'entità customer denominata "Ben Smith" e, se la trova, la elimina.

    // Create a retrieve operation that expects a customer entity.
    TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

    // Execute the operation.
    TableResult retrievedResult = peopleTable.Execute(retrieveOperation);

    // Assign the result to a CustomerEntity object.
    CustomerEntity deleteEntity = (CustomerEntity)retrievedResult.Result;

    // Create the Delete TableOperation and then execute it.
    if (deleteEntity != null)
    {
       TableOperation deleteOperation = TableOperation.Delete(deleteEntity);

       // Execute the operation.
       await peopleTable.ExecuteAsync(deleteOperation);

       Console.WriteLine("Entity deleted.");
    }

    else
       Console.WriteLine("Couldn't delete the entity.");

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]




<!--HONumber=Nov16_HO3-->


