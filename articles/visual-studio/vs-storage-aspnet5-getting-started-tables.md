---
title: Introduzione all'archiviazione tabelle e ai servizi connessi di Visual Studio (ASP.NET Core) |Microsoft Docs
description: Informazioni su come iniziare con il servizio di archiviazione tabelle di Azure in un progetto ASP.NET Core in Visual Studio dopo aver eseguito la connessione a un account di archiviazione con i servizi connessi di Visual Studio
services: storage
documentationcenter: 
author: kraigb
manager: ghogen
editor: 
ms.assetid: c3c451d1-71ff-4222-a348-c41c98a02b85
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 11/14/2017
ms.author: kraigb
ms.openlocfilehash: 81f0304850a108fc688dd862ff5ab677d6ebc28e
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/28/2017
---
# <a name="how-to-get-started-with-azure-table-storage-and-visual-studio-connected-services"></a>Introduzione all'archiviazione tabelle di Azure e ai servizi relativi a Visual Studio

[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

Questo articolo descrive come iniziare a usare Archiviazione tabelle di Azure in Visual Studio dopo aver creato o fatto riferimento a un account di archiviazione di Azure in un progetto ASP.NET Core usando la funzionalità **Servizi connessi** di Visual Studio. **Servizi connessi** consente di installare i pacchetti NuGet appropriati per accedere all'archiviazione di Azure nel progetto e di aggiungere la stringa di connessione per l'account di archiviazione ai file di configurazione del progetto. Per informazioni generali su Archiviazione di Azure, vedere la relativa [documentazione](https://azure.microsoft.com/documentation/services/storage/).

Il servizio di archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un datastore NoSQL che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali. Per ulteriori informazioni generali sull'utilizzo dell'archiviazione tabelle di Azure, vedere [Introduzione all'archiviazione tabelle di Azure con .NET](../storage/storage-dotnet-how-to-use-tables.md).

Per iniziare, creare una tabella nell'account di archiviazione. Questo articolo illustrerà come creare una tabella in C# e come eseguire le operazioni di base relative alle tabelle, ad esempio l'aggiunta, la modifica, la lettura e la rimozione di voci nelle tabelle.  Il codice usa la Libreria client di archiviazione di Azure per .NET. Per ulteriori informazioni su ASP.NET, vedere [ASP.NET](http://www.asp.net).

Alcune API di Archiviazione di Azure sono asincrone e il codice riportato in questo articolo presuppone che vengano usati metodi asincroni. Per altre informazioni, vedere [Programmazione asincrona](https://docs.microsoft.com/dotnet/csharp/async).

## <a name="access-tables-in-code"></a>Accesso alle tabelle nel codice

Per accedere alle tabelle nei progetti ASP.NET Core, è necessario includere gli elementi seguenti ai file di origine C# che consentono di accedere all'archiviazione tabelle di Azure.

1. Aggiungere le necessarie istruzioni `using`:

    ```cs
    using Microsoft.Framework.Configuration;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Table;
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```

1. Ottenere un oggetto `CloudStorageAccount` che rappresenta le informazioni sull'account di archiviazione. Usare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure:

    ```cs
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));
    ```

1. Ottenere un oggetto `CloudTableClient` per fare riferimento agli oggetti delle tabelle nell'account di archiviazione:

    ```cs
    // Create the table client.
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient();
    ```

1. Ottenere un oggetto `CloudTable` per fare riferimento a una tabelle specifica e alle relative entità:

    ```cs
    // Get a reference to a table named "peopleTable"
    CloudTable table = tableClient.GetTableReference("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>Creazione di una tabella in codice

Per creare la tabella di Azure, chiamare ``CreateIfNotExistsAsync()`:

```cs
// Create the CloudTable if it does not exist
await table.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>Aggiungere un'entità a una tabella

Per aggiungere un'entità a una tabella, creare una classe che definisca le proprietà dell'entità. Il codice seguente definisce una classe di entità denominata `CustomerEntity` che utilizza il nome e il cognome del cliente rispettivamente come chiave di riga e chiave di partizione.

```cs
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

Per eseguire operazioni su tabelle che interessano le entità, viene usato l'oggetto `CloudTable`creato in precedenza nella sezione [Accesso alle tabelle nel codice](#access-tables-in-code). L'oggetto `TableOperation` rappresenta l'operazione da eseguire. L'esempio di codice seguente mostra come creare un oggetto `CloudTable` e un oggetto `CustomerEntity`. Per preparare l'operazione, viene creato un oggetto `TableOperation` per inserire l'entità customer nella tabella. Infine, per eseguire l'operazione viene chiamato `CloudTable.ExecuteAsync`.

```cs
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Create the TableOperation that inserts the customer entity.
TableOperation insertOperation = TableOperation.Insert(customer1);

// Execute the insert operation.
await peopleTable.ExecuteAsync(insertOperation);
```

## <a name="insert-a-batch-of-entities"></a>Inserire un batch di entità

È possibile inserire più entità in una tabella in una singola operazione di scrittura. L'esempio di codice seguente crea due oggetti entità ("Jeff Smith" e "Ben Smith"), li aggiunge a un oggetto `TableBatchOperation` usando il metodo `Insert` e quindi avvia l'operazione chiamando `CloudTable.ExecuteBatchAsync`.

```cs
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
```

## <a name="get-all-of-the-entities-in-a-partition"></a>Ottenere tutte le entità di una partizione

Per eseguire una query su una tabella e recuperare tutte le entità di una partizione, usare un oggetto `TableQuery`. Nell'esempio di codice seguente viene specificato un filtro per le entità in cui la chiave di partizione è 'Smith'. Questo esempio consente di stampare sulla console i campi di ogni entità inclusa nei risultati della query.

```cs
// Construct the query operation for all customer entities where PartitionKey="Smith".
TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

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
```

## <a name="get-a-single-entity"></a>Ottenere una singola entità

È possibile scrivere una query per ottenere una singola entità specifica. Il codice seguente usa un oggetto `TableOperation` per specificare un cliente denominato 'Ben Smith'. Questa procedura restituisce una sola entità anziché una raccolta e il valore restituito in `TableResult.Result` è un oggetto `CustomerEntity`. Il modo più rapido per recuperare una singola entità dal servizio `Table` è quello di specificare in una query sia la chiave di partizione che quella di riga.

```cs
// Create a retrieve operation that takes a customer entity.
TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");

// Execute the retrieve operation.
TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);

// Print the phone number of the result.
if (retrievedResult.Result != null)
   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
else
   Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>Eliminare un'entità

È possibile eliminare un'entità dopo averla individuata. Il codice seguente cerca ed elimina un'entità customer denominata "Ben Smith":

```cs
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
```

## <a name="next-steps"></a>Passaggi successivi
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
