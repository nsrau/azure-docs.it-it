<properties 
	pageTitle="Introduzione all'archiviazione tabelle di Azure e ai servizi relativi a Visual Studio" 
	description="Informazioni su come iniziare a usare l'archiviazione tabelle di Azure in un progetto ASP.NET in Visual Studio." 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/04/2015" 
	ms.author="patshea123"/>

# Introduzione all'archiviazione tabelle di Azure e ai servizi relativi a Visual Studio

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-aspnet-getting-started-tables.md)
> - [What Happened](vs-storage-aspnet-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet-getting-started-queues.md)
> - [Tables](vs-storage-aspnet-getting-started-tables.md)

## Panoramica
In questo articolo viene descritto come iniziare a utilizzare l'archiviazione delle tabelle di Azure in Visual Studio dopo aver creato o fatto riferimento a un account di archiviazione di Azure in un progetto ASP.NET 5 usando la finestra di dialogo **Aggiungi servizi connessi** di Visual Studio. In questo articolo viene illustrato come eseguire attività comuni nelle tabelle di Azure, incluse la creazione e l'eliminazione di una tabella, nonché come funzionano le entità di tabella. Negli esempi, scritti in codice C#, viene utilizzata la [libreria del client di archiviazione di Azure per .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx). Per ulteriori informazioni generali sull'utilizzo dell'archiviazione delle tabelle di Azure, vedere [Come utilizzare l'archiviazione delle tabelle da .NET](storage-dotnet-how-to-use-tables.md).


Il servizio di archiviazione tabelle di Azure consente di archiviare grandi quantità di dati strutturati. Il servizio è un datastore NoSQL che accetta chiamate autenticate dall'interno e dall'esterno del cloud di Azure. Le tabelle di Azure sono ideali per l'archiviazione di dati strutturati non relazionali.




##Accesso alle tabelle nel codice 

1. Assicurarsi che le dichiarazioni dello spazio dei nomi all'inizio del file C# includano queste istruzioni `using`.

	using Microsoft.Azure; using Microsoft.WindowsAzure.Storage; using Microsoft.WindowsAzure.Storage.Auth; using Microsoft.WindowsAzure.Storage.Table;

2. Ottenere un oggetto `CloudStorageAccount` che rappresenta le informazioni sull'account di archiviazione. Utilizzare il codice seguente per ottenere la stringa di connessione di archiviazione e le informazioni sull'account di archiviazione dalla configurazione del servizio di Azure.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **NOTA:** utilizzare tutto il codice riportato in precedenza prima del codice indicato negli esempi seguenti.

3. Ottenere un oggetto `CloudTableClient` per fare riferimento agli oggetti delle tabelle nell'account di archiviazione.

	    // Create the table client.
    	CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

4. Ottenere un oggetto di riferimento `CloudTable` per fare riferimento a tabelle ed entità specifiche.
	
    	// Get a reference to a table named "peopleTable"
	    CloudTable table = tableClient.GetTableReference("peopleTable");

##Creazione di una tabella in codice

Per creare tabelle di Azure, è sufficiente aggiungere una chiamata a `CreateIfNotExistsAsync()` al codice precedente.

	// Create the CloudTable if it does not exist
	await table.CreateIfNotExistsAsync();


##Inserire un batch di entità

È possibile inserire più entità in una tabella in una singola operazione di scrittura. L'esempio di codice seguente crea due oggetti entità ("Jeff Smith" e "Ben Smith"), li aggiunge a un oggetto `TableBatchOperation` usando il metodo Insert e quindi avvia l'operazione chiamando `CloudTable.ExecuteBatchAsync`.

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"
	
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

##Ottenere tutte le entità di una partizione
Per eseguire una query su una tabella e recuperare tutte le entità di una partizione, usare un oggetto `TableQuery`. Nell'esempio di codice seguente viene specificato un filtro per le entità in cui la chiave di partizione è 'Smith'. Questo esempio consente di stampare sulla console i campi di ogni entità inclusa nei risultati della query.

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

	// Construct the query operation for all customer entities where PartitionKey="Smith".
    TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>().Where(TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, "Smith"));

    // Print the fields for each customer.
    TableContinuationToken token = null;
    do
    {
    	TableQuerySegment<CustomerEntity>
        resultSegment = await peopleTable.ExecuteQuerySegmentedAsync(query, token);
        token = resultSegment.ContinuationToken;

        foreach (CustomerEntity entity in resultSegment.Results)
        {
        Console.WriteLine("{0}, {1}\t{2}\t{3}", entity.PartitionKey, entity.RowKey,
        entity.Email, entity.PhoneNumber);
        }
        } while (token != null);

        return View();


##Ottenere una singola entità
È possibile scrivere una query per ottenere una singola entità specifica. Il codice seguente usa un oggetto `TableOperation` per specificare un cliente denominato 'Ben Smith'. Questo metodo restituisce una sola entità anziché una raccolta, e il valore restituito in `TableResult.Result` è un oggetto `CustomerEntity`. La specifica delle chiavi di partizione e di riga in una query costituisce la soluzione più rapida per recuperare una singola entità dal servizio tabelle.

        // Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"

        // Create a retrieve operation that takes a customer entity.
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>("Smith", "Ben");
	
	// Execute the retrieve operation.
	TableResult retrievedResult = await peopleTable.ExecuteAsync(retrieveOperation);
	
	// Print the phone number of the result.
	if (retrievedResult.Result != null)
	   Console.WriteLine(((CustomerEntity)retrievedResult.Result).PhoneNumber);
	else
	   Console.WriteLine("The phone number could not be retrieved.");

##Eliminare un'entità
È possibile eliminare un'entità dopo averla individuata. Il codice seguente cerca un'entità customer denominata "Ben Smith" e, se la trova, la elimina.

	// Get a reference to a CloudTable object named 'peopleTable' as described in "Access a table in code"
	
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

## Passaggi successivi

[AZURE.INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]



  

<!---HONumber=August15_HO6-->