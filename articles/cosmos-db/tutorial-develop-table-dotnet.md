---
title: Introduzione all'API Tabella di Azure Cosmos DB con .NET Standard SDK
description: Informazioni su come archiviare ed eseguire query sui dati strutturati nell'account dell'API Tabella di Azure Cosmos DB
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: sample
ms.date: 12/03/2019
ms.openlocfilehash: 0a1ba00d2d24664590f76438a90f651e0826d419
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870548"
---
# <a name="get-started-with-azure-cosmos-db-table-api-and-azure-table-storage-using-the-net-sdk"></a>Introduzione all'API Tabella di Azure Cosmos DB e all'archiviazione tabelle con .NET SDK

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]

[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

È possibile usare l'API Tabella di Azure Cosmos DB o l'archiviazione tabelle di Azure per archiviare dati NoSQL strutturati nel cloud, fornendo un archivio di chiavi/attributi con una progettazione senza schema. Dato che l'API Tabella di Azure Cosmos DB e l'archiviazione tabelle sono senza schema, è facile adattare i dati con il variare delle esigenze dell'applicazione. È possibile usare l'API Tabella di Azure Cosmos DB o l'archiviazione tabelle per archiviare set di dati flessibili, ad esempio i dati utente per le applicazioni Web, le rubriche, le informazioni sui dispositivi o altri tipi di metadati richiesti dal servizio. 

Questa esercitazione descrive un esempio che illustra come usare la [libreria di tabelle Microsoft Azure Cosmos DB per .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) con l'API Tabella di Azure Cosmos DB e l'archiviazione tabelle di Azure. È necessario usare la connessione specifica per il servizio di Azure. Questi scenari vengono esaminati usando esempi C# che illustrano come creare tabelle, inserire/aggiornare i dati, eseguire query sui dati ed eliminare le tabelle.

## <a name="prerequisites"></a>Prerequisiti

Per completare correttamente l'esempio sono necessari gli elementi seguenti:

* [Microsoft Visual Studio](https://www.visualstudio.com/downloads/)

* [Libreria di tabelle Microsoft Azure CosmosDB per .NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): questa libreria è attualmente disponibile per .NET Standard e .NET Framework. 

* [Account API Tabella di Azure Cosmos DB](create-table-dotnet.md#create-a-database-account).

## <a name="create-an-azure-cosmos-db-table-api-account"></a>Creare un account per l'API di tabella di Azure Cosmos DB

[!INCLUDE [cosmos-db-create-dbaccount-table](../../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="create-a-net-console-project"></a>Creare un progetto di console .NET

In Visual Studio creare una nuova applicazione console .NET. La procedura seguente illustra come creare un'applicazione console in Visual Studio 2019. È possibile usare la libreria di tabelle di Azure Cosmos DB in qualsiasi tipo di applicazione .NET, ad esempio un servizio cloud o un'app Web di Azure, nonché in applicazioni desktop e per dispositivi mobili. Per semplicità, in questa guida si usa un'applicazione console.

1. Selezionare **File** > **Nuovo** > **Progetto**.

1. Scegliere **App console (.NET Core)** , quindi selezionare **Avanti**.

1. Nel campo **Nome progetto** immettere un nome per l'applicazione, ad esempio **CosmosTableSamples**. (È possibile immettere un nome diverso in base alle esigenze).

1. Selezionare **Create** (Crea).

Tutti gli esempi di codice in questo esempio possono essere aggiunti al metodo Main() del file **Program.cs** dell'applicazione console.

## <a name="install-the-required-nuget-package"></a>Installare il pacchetto NuGet necessario

Per ottenere il pacchetto NuGet, seguire questa procedura:

1. Fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Gestisci pacchetti NuGet**.

1. Cercare online `Microsoft.Azure.Cosmos.Table`, `Microsoft.Extensions.Configuration`, `Microsoft.Extensions.Configuration.Json`, `Microsoft.Extensions.Configuration.Binder` e selezionare **Install** (Installa) per installare la libreria di tabelle Microsoft Azure Cosmos DB.

## <a name="configure-your-storage-connection-string"></a>Configurare la stringa di connessione di archiviazione

1. Dal [portale di Azure](https://portal.azure.com/) passare all'account Azure Cosmos o all'account di archiviazione tabelle. 

1. Aprire il riquadro **Stringa di connessione** oppure **Chiavi di accesso**. Usare il pulsante di copia sul lato destro della finestra per copiare la **STRINGA DI CONNESSIONE PRIMARIA**.

   ![Visualizzare e copiare la STRINGA DI CONNESSIONE PRIMARIA nel riquadro Stringa di connessione](./media/create-table-dotnet/connection-string.png)
   
1. Per configurare la stringa di connessione, in Visual Studio fare clic con il pulsante destro del mouse sul progetto **CosmosTableSamples**.

1. Scegliere **Aggiungi** e quindi **Nuovo elemento**. Creare un nuovo file **Settings.json** con tipo **File di configurazione JSON per TypeScript**. 

1. Sostituire il codice nel file Settings.json con il codice seguente e assegnare la stringa di connessione principale:

   ```csharp
   {
   "StorageConnectionString": <Primary connection string of your Azure Cosmos DB account>
   }
   ```

1. Fare clic con il pulsante destro del mouse sul progetto **CosmosTableSamples**. Scegliere **Aggiungi**, **Nuovo elemento** e aggiungere una classe denominata **AppSettings.cs**.

1. Aggiungere il codice seguente al file AppSettings.cs. Questo file legge la stringa di connessione dal file Settings.json e la assegna al parametro di configurazione:

   ```csharp
   namespace CosmosTableSamples
   {
    using Microsoft.Extensions.Configuration;
    public class AppSettings
    {
        public string StorageConnectionString { get; set; }
        public static AppSettings LoadAppSettings()
        {
            IConfigurationRoot configRoot = new ConfigurationBuilder()
                .AddJsonFile("Settings.json")
                .Build();
            AppSettings appSettings = configRoot.Get<AppSettings>();
            return appSettings;
        }
    }
   }
   ```

## <a name="parse-and-validate-the-connection-details"></a>Analizzare e convalidare i dettagli della connessione 

1. Fare clic con il pulsante destro del mouse sul progetto **CosmosTableSamples**. Scegliere **Aggiungi**, **Nuovo elemento** e aggiungere una classe denominata **Common.cs**. Si scriverà il codice per convalidare i dettagli della connessione e creare una tabella in questa classe.

1. Definire un metodo `CreateStorageAccountFromConnectionString` come mostrato di seguito. Questo metodo analizzerà i dettagli della stringa di connessione e verificherà che il nome dell'account e i dettagli delle chiavi dell'account specificati nel file "Settings.json" siano validi. 

 ```csharp
using System;

namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Microsoft.Azure.Documents;

    public class Common
    {
        public static CloudStorageAccount CreateStorageAccountFromConnectionString(string storageConnectionString)
        {
            CloudStorageAccount storageAccount;
            try
            {
                storageAccount = CloudStorageAccount.Parse(storageConnectionString);
            }
            catch (FormatException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the application.");
                throw;
            }
            catch (ArgumentException)
            {
                Console.WriteLine("Invalid storage account information provided. Please confirm the AccountName and AccountKey are valid in the app.config file - then restart the sample.");
                Console.ReadLine();
                throw;
            }

            return storageAccount;
        }
    }
}
   ```


## <a name="create-a-table"></a>Creare una tabella 

La classe [CloudTableClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtableclient) consente di recuperare le tabelle e le entità archiviate nell'archivio tabelle. Dato che non sono presenti tabelle nell'account API Tabella di Cosmos DB, si aggiungerà il metodo `CreateTableAsync` alla classe **Common.cs** per creare una tabella:

```csharp
public static async Task<CloudTable> CreateTableAsync(string tableName)
  {
    string storageConnectionString = AppSettings.LoadAppSettings().StorageConnectionString;

    // Retrieve storage account information from connection string.
    CloudStorageAccount storageAccount = CreateStorageAccountFromConnectionString(storageConnectionString);

    // Create a table client for interacting with the table service
    CloudTableClient tableClient = storageAccount.CreateCloudTableClient(new TableClientConfiguration());

    Console.WriteLine("Create a Table for the demo");

    // Create a table client for interacting with the table service 
    CloudTable table = tableClient.GetTableReference(tableName);
    if (await table.CreateIfNotExistsAsync())
    {
      Console.WriteLine("Created Table named: {0}", tableName);
    }
    else
    {
      Console.WriteLine("Table {0} already exists", tableName);
    }

    Console.WriteLine();
    return table;
}
```

## <a name="define-the-entity"></a>Definire l'entità 

Per eseguire il mapping di entità a oggetti C#, viene utilizzata una classe personalizzata derivata da [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Per aggiungere un'entità a una classe, creare una classe che definisca le proprietà dell'entità.

Fare clic con il pulsante destro del mouse sul progetto **CosmosTableSamples**. Scegliere **Aggiungi**, **Nuova cartella** e assegnare il nome **Model**. All'interno della cartella Model aggiungere una classe denominata **CustimerEntity.cs** e aggiungere il codice seguente alla classe.

```csharp
namespace CosmosTableSamples.Model
{
    using Microsoft.Azure.Cosmos.Table;
    public class CustomerEntity : TableEntity
    {
        public CustomerEntity()
        {
        }

        public CustomerEntity(string lastName, string firstName)
        {
            PartitionKey = lastName;
            RowKey = firstName;
        }

        public string Email { get; set; }
        public string PhoneNumber { get; set; }
    }
}
```

Questo codice consente di definire una classe di entità che usa il nome e il cognome del cliente rispettivamente come chiave di riga e chiave di partizione. La partizione e la chiave di riga di un'entità consentono di identificare in modo univoco l'entità nella tabella. Le query su entità con la stessa chiave di partizione vengono eseguite più rapidamente di quelle con chiavi di partizione diverse, tuttavia l'uso di chiavi di partizione diverse assicura una maggiore scalabilità delle operazioni parallele. Le entità da archiviare nelle tabelle devono essere di un tipo supportato, ad esempio derivato dalla classe [TableEntity](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableentity). Le proprietà dell'entità da archiviare in una tabella devono essere proprietà pubbliche del tipo e supportare sia l'ottenimento che l'impostazione di valori. Il tipo di entità deve inoltre esporre un costruttore senza parametri.

## <a name="insert-or-merge-an-entity"></a>Inserire o unire un'entità

Il codice di esempio seguente crea un oggetto entità e lo aggiunge alla tabella. Il metodo InsertOrMerge nella classe [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation) viene usato per inserire o unire un'entità. Il metodo [CloudTable.ExecuteAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.cloudtable.executeasync?view=azure-dotnet) viene chiamato per eseguire l'operazione. 

Fare clic con il pulsante destro del mouse sul progetto **CosmosTableSamples**. Scegliere **Aggiungi**, **Nuovo elemento** e aggiungere una classe denominata **SamplesUtils.cs**. Questa classe archivia tutto il codice richiesto per eseguire operazioni CRUD sulle entità. 

```csharp
 public static async Task<CustomerEntity> InsertOrMergeEntityAsync(CloudTable table, CustomerEntity entity)
 {
     if (entity == null)
     {
         throw new ArgumentNullException("entity");
     }
     try
     {
         // Create the InsertOrReplace table operation
         TableOperation insertOrMergeOperation = TableOperation.InsertOrMerge(entity);

         // Execute the operation.
         TableResult result = await table.ExecuteAsync(insertOrMergeOperation);
         CustomerEntity insertedCustomer = result.Result as CustomerEntity;

         // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure Cosmos DB
         if (result.RequestCharge.HasValue)
         {
             Console.WriteLine("Request Charge of InsertOrMerge Operation: " + result.RequestCharge);
         }

         return insertedCustomer;
     }
     catch (StorageException e)
     {
         Console.WriteLine(e.Message);
         Console.ReadLine();
         throw;
     }
 }
```

### <a name="get-an-entity-from-a-partition"></a>Ottenere un'entità da una partizione

È possibile ottenere entità da una partizione usando il metodo Retrieve nella classe [TableOperation](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.table.tableoperation). Il codice di esempio seguente ottiene la chiave di partizione, la chiave di riga l'indirizzo e-mail e il numero di telefono di un'entità cliente. Questo esempio stampa anche le unità richiesta utilizzate per eseguire query per l'entità. Per eseguire query per l'entità, aggiungere il codice seguente al file **SamplesUtils.cs**: 

```csharp
public static async Task<CustomerEntity> RetrieveEntityUsingPointQueryAsync(CloudTable table, string partitionKey, string rowKey)
    {
      try
      {
        TableOperation retrieveOperation = TableOperation.Retrieve<CustomerEntity>(partitionKey, rowKey);
        TableResult result = await table.ExecuteAsync(retrieveOperation);
        CustomerEntity customer = result.Result as CustomerEntity;
        if (customer != null)
        {
          Console.WriteLine("\t{0}\t{1}\t{2}\t{3}", customer.PartitionKey, customer.RowKey, customer.Email, customer.PhoneNumber);
        }

        // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
        if (result.RequestCharge.HasValue)
        {
           Console.WriteLine("Request Charge of Retrieve Operation: " + result.RequestCharge);
        }

        return customer;
        }
        catch (StorageException e)
        {
           Console.WriteLine(e.Message);
           Console.ReadLine();
           throw;
        }
    }
```

## <a name="delete-an-entity"></a>Eliminare un'entità

Per eliminare facilmente un'entità dopo averla recuperata, è possibile usare lo stesso modello illustrato per aggiornare un'entità. Il codice seguente consente di recuperare ed eliminare un'entità customer. Per eliminare un'entità, aggiungere il codice seguente al file **SamplesUtils.cs**: 

```csharp
public static async Task DeleteEntityAsync(CloudTable table, CustomerEntity deleteEntity)
   {
     try
     {
        if (deleteEntity == null)
     {
        throw new ArgumentNullException("deleteEntity");
     }

    TableOperation deleteOperation = TableOperation.Delete(deleteEntity);
    TableResult result = await table.ExecuteAsync(deleteOperation);

    // Get the request units consumed by the current operation. RequestCharge of a TableResult is only applied to Azure CosmoS DB 
    if (result.RequestCharge.HasValue)
    {
       Console.WriteLine("Request Charge of Delete Operation: " + result.RequestCharge);
    }

    }
    catch (StorageException e)
    {
        Console.WriteLine(e.Message);
        Console.ReadLine();
        throw;
    }
}
```

## <a name="execute-the-crud-operations-on-sample-data"></a>Eseguire le operazioni CRUD sui dati di esempio

Dopo aver definito i metodi per creare tabelle e inserire o unire entità, eseguire questi metodi sui dati di esempio. A tale scopo, fare clic con il pulsante destro del mouse sul progetto **CosmosTableSamples**. Selezionare **Aggiungi**, **Nuovo elemento** e aggiungere una classe denominata **BasicSamples.cs**, quindi aggiungere il codice seguente alla classe. Questo codice crea una tabella e aggiunge entità al suo interno. Se si vuole eliminare l'entità e la tabella alla fine del progetto, rimuovere i commenti dai metodi `table.DeleteIfExistsAsync()` e `SamplesUtils.DeleteEntityAsync(table, customer)` nel codice seguente:

```csharp
using System;
namespace CosmosTableSamples
{
    using System.Threading.Tasks;
    using Microsoft.Azure.Cosmos.Table;
    using Model;

    class BasicSamples
    {
        public async Task RunSamples()
        {
            Console.WriteLine("Azure Cosmos DB Table - Basic Samples\n");
            Console.WriteLine();

            string tableName = "demo" + Guid.NewGuid().ToString().Substring(0, 5);

            // Create or reference an existing table
            CloudTable table = await Common.CreateTableAsync(tableName);

            try
            {
                // Demonstrate basic CRUD functionality 
                await BasicDataOperationsAsync(table);
            }
            finally
            {
                // Delete the table
                // await table.DeleteIfExistsAsync();
            }
        }

        private static async Task BasicDataOperationsAsync(CloudTable table)
        {
            // Create an instance of a customer entity. See the Model\CustomerEntity.cs for a description of the entity.
            CustomerEntity customer = new CustomerEntity("Harp", "Walter")
            {
                Email = "Walter@contoso.com",
                PhoneNumber = "425-555-0101"
            };

            // Demonstrate how to insert the entity
            Console.WriteLine("Insert an Entity.");
            customer = await SamplesUtils.InsertOrMergeEntityAsync(table, customer);

            // Demonstrate how to Update the entity by changing the phone number
            Console.WriteLine("Update an existing Entity using the InsertOrMerge Upsert Operation.");
            customer.PhoneNumber = "425-555-0105";
            await SamplesUtils.InsertOrMergeEntityAsync(table, customer);
            Console.WriteLine();

            // Demonstrate how to Read the updated entity using a point query 
            Console.WriteLine("Reading the updated Entity.");
            customer = await SamplesUtils.RetrieveEntityUsingPointQueryAsync(table, "Harp", "Walter");
            Console.WriteLine();

            // Demonstrate how to Delete an entity
            //Console.WriteLine("Delete the entity. ");
            //await SamplesUtils.DeleteEntityAsync(table, customer);
            //Console.WriteLine();
        }
    }
}
```

Il codice precedente crea una tabella che inizia con "demo" e il GUID generato viene aggiunto al nome della tabella. Aggiunge quindi un'entità cliente con nome e cognome "Harp Walter" e in seguito aggiorna il numero di telefono di questo utente. 

In questa esercitazione è stato compilato il codice per eseguire operazioni CRUD di base sui dati archiviati nell'account API Tabella. È anche possibile eseguire operazioni avanzate quali inserimento di dati in batch, query su tutti i dati all'interno di una partizione, query su un intervallo di dati all'interno di una partizione, elencare le tabelle nell'account i cui nomi iniziano con il prefisso specificato. È possibile scaricare l'esempio completo dal repository GitHub [azure-cosmos-table-dotnet-core-getting-started](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started). La classe [AdvancedSamples.cs](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started/blob/master/CosmosTableSamples/AdvancedSamples.cs) ha più operazioni che è possibile eseguire sui dati.  

## <a name="run-the-project"></a>Eseguire il progetto

Nel progetto **CosmosTableSamples**. Aprire la classe denominata **Program.cs** e aggiungervi il codice seguente per chiamare BasicSamples durante l'esecuzione del progetto.

```csharp
using System;

namespace CosmosTableSamples
{
    class Program
    {
        public static void Main(string[] args)
        {
            Console.WriteLine("Azure Cosmos Table Samples");
            BasicSamples basicSamples = new BasicSamples();
            basicSamples.RunSamples().Wait();
           
            Console.WriteLine();
            Console.WriteLine("Press any key to exit");
            Console.Read();
        }
    }
}
```

Compilare ora la soluzione e premere F5 per eseguire il progetto. Quando viene eseguito il progetto, nel prompt dei comandi verrà visualizzato l'output seguente:

![Output del prompt dei comandi](./media/tutorial-develop-table-standard/output-from-sample.png)

Se viene visualizzato un errore che indica che non è stato trovato il file Settings.json durante l'esecuzione del progetto, è possibile risolvere il problema aggiungendo la voce XML seguente alle impostazioni del progetto. Fare clic con il pulsante destro del mouse su CosmosTableSamples, scegliere il comando di modifica di CosmosTableSamples.csproj e aggiungere l'elemento itemGroup seguente: 

```csharp
  <ItemGroup>
    <None Update="Settings.json">
      <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
  </ItemGroup>
```
Ora è possibile accedere al portale di Azure e verificare che i dati siano presenti nella tabella. 

![Risultati nel portale](./media/tutorial-develop-table-standard/results-in-portal.png)

## <a name="next-steps"></a>Passaggi successivi

È ora possibile passare all'esercitazione successiva per informazioni su come eseguire la migrazione dei dati all'account API Tabella di Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Come eseguire query sui dati](../cosmos-db/table-import.md)
