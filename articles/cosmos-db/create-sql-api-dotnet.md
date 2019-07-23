---
title: Compilare un'app console .NET per gestire le risorse dell'API SQL di Azure Cosmos DB
description: Informazioni su come compilare un'app console .NET per gestire le risorse dell'account API SQL di Azure Cosmos DB.
author: SnehaGunda
ms.author: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/12/2019
ms.openlocfilehash: 1687a81952418c0b751fe0b9ec0ec560ae0b2b2b
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297602"
---
# <a name="quickstart-build-a-net-console-app-to-manage-azure-cosmos-db-sql-api-resources"></a>Guida introduttiva: Compilare un'app console .NET per gestire le risorse dell'API SQL di Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](create-sql-api-dotnet.md)
> * [Java](create-sql-api-java.md)
> * [Node.js](create-sql-api-nodejs.md)
> * [Python](create-sql-api-python.md)
> * [Xamarin](create-sql-api-xamarin-dotnet.md)
>  

Per iniziare a usare la libreria client dell'API SQL di Azure Cosmos DB per .NET, seguire i passaggi descritti in questo documento per installare il pacchetto .NET, compilare un'app e provare il codice di esempio per le operazioni CRUD di base sui dati archiviati in Azure Cosmos DB. 

Azure Cosmos DB è il servizio di database di Microsoft multimodello distribuito a livello globale. È possibile usare Azure Cosmos DB per creare rapidamente database di chiavi/valori, di documenti e a grafo ed eseguire query su di essi. Usare la libreria client dell'API SQL di Azure Cosmos DB per .NET per:

* Creare un database e un contenitore Azure Cosmos
* Aggiungere dati di esempio al contenitore
* Eseguire query sui dati 
* eliminare il database

[Documentazione di riferimento dell'API](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet) | [Codice sorgente della libreria](https://github.com/Azure/azure-cosmos-dotnet-v3) | [Pacchetto (NuGet)](https://www.nuget.org/packages/Microsoft.Azure.Cosmos)

## <a name="prerequisites"></a>Prerequisiti

* Sottoscrizione di Azure: [crearne una gratuitamente](https://azure.microsoft.com/free/) oppure [provare gratuitamente Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) senza una sottoscrizione di Azure e senza impegno. 
* [.NET Core 2.1 SDK o versione successiva](https://dotnet.microsoft.com/download/dotnet-core/2.1).
* [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest)

## <a name="setting-up"></a>Configurazione

Questa sezione illustra la creazione di un account Azure Cosmos e la configurazione di un progetto che usa la libreria client dell'API SQL di Azure Cosmos DB per .NET per gestire le risorse. Il codice di esempio descritto in questo articolo crea un database `FamilyDatabase` e i membri della famiglia (ogni membro della famiglia è un elemento) all'interno del database. Ogni membro della famiglia ha proprietà come `Id, FamilyName, FirstName, LastName, Parents, Children, Address,`. La proprietà `LastName` viene usata come chiave di partizione per il contenitore. 

### <a id="create-account"></a>Creare un account Azure Cosmos

Il codice seguente creerà un account Azure Cosmos con coerenza di sessione. L'account viene replicato in `South Central US` e `North Central US`. Selezionare il pulsante **Prova**, incollare il codice per eseguirlo in Azure Cloud Shell. 

```azurecli-interactive

# Set variables for the new SQL API account, database, and container
resourceGroupName='myResourceGroup'
location='southcentralus'
accountName='mysqlapicosmosdb' 
databaseName='FamilyDatabase'
containerName='FamilyContainer'

# Create a resource group
az group create \
    --name $resourceGroupName \
    --location $location

# Create a SQL API Cosmos DB account with session consistency and multi-master enabled
az cosmosdb create \
    --resource-group $resourceGroupName \
    --name $accountName \
    --kind GlobalDocumentDB \
    --locations regionName="South Central US" failoverPriority=0 --locations regionName="North Central US" failoverPriority=1 \
    --default-consistency-level "Session" \
    --enable-multiple-write-locations true

```

### <a id="create-dotnet-core-app"></a>Creare una nuova app .NET

Creare una nuova applicazione .NET nell'ambiente di sviluppo integrato o nell'editor preferito. In una finestra della console eseguire il comando dotnet new seguente per creare una nuova app con il nome `todo`.

```console
dotnet new console --langVersion 7.1 -n todo
```

Spostarsi nella cartella dell'app appena creata. È possibile compilare l'applicazione con il comando seguente:

```console
cd todo
dotnet build
```

L'output previsto al termine della creazione dovrebbe essere simile al seguente:

```console
  Restore completed in 100.37 ms for C:\Users\user1\Downloads\CosmosDB_Samples\todo\todo.csproj.
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.dll
  todo -> C:\Users\user1\Downloads\CosmosDB_Samples\todo\bin\Debug\netcoreapp2.2\todo.Views.dll

Build succeeded.
    0 Warning(s)
    0 Error(s)

Time Elapsed 00:00:34.17
```

### <a id="install-package"></a>Installare il pacchetto di Azure Cosmos DB

Sempre nella directory dell'applicazione, installare la libreria client di Azure Cosmos DB per .NET Core usando il comando dotnet add package.

```console
dotnet add package Microsoft.Azure.Cosmos
```

### <a name="copy-your-azure-cosmos-account-credentials-from-the-azure-portal"></a>Copiare le credenziali dell'account Azure Cosmos dal portale di Azure

L'applicazione di esempio deve eseguire l'autenticazione all'account Azure Cosmos. Per eseguire l'autenticazione, è necessario passare le credenziali dell'account Azure Cosmos all'applicazione. Ottenere le credenziali dell'account Azure Cosmos seguendo questa procedura:

1. Accedere al [portale di Azure](https://portal.azure.com/).

1. Accedere all'account Azure Cosmos. 

1. Aprire il riquadro **Chiavi** e copiare l'**URI** e la **CHIAVE PRIMARIA** dell'account. I valori dell'URI e delle chiavi verranno aggiunti a una variabile di ambiente nel passaggio successivo.

### <a name="set-the-environment-variables"></a>Impostare le variabili di ambiente

Dopo aver copiato l'**URI** e la **CHIAVE PRIMARIA** dell'account, salvarli in una nuova variabile di ambiente nel computer locale che esegue l'applicazione. Per impostare la variabile di ambiente, aprire una finestra della console ed eseguire il comando seguente. Assicurarsi di sostituire i valori `<Your_Azure_Cosmos_account_URI>` e `<Your_Azure_Cosmos_account_PRIMARY_KEY>`.

```console
setx EndpointUrl <Your_Azure_Cosmos_account_URI>
setx PrimaryKey <Your_Azure_Cosmos_account_PRIMARY_KEY>
```

 ## <a id="object-model"></a>Modello a oggetti

Prima di iniziare a compilare l'applicazione, verranno esaminati la gerarchia di risorse in Azure Cosmos DB e il modello a oggetti usato per creare e accedere a queste risorse. Azure Cosmos DB crea le risorse nell'ordine seguente:

* Account Azure Cosmos 
* Database 
* Contenitori 
* Items

Per altre informazioni sulla gerarchia delle diverse entità, vedere l'articolo sull'[uso di database, contenitori ed elementi in Azure Cosmos DB](databases-containers-items.md). Per interagire con queste risorse, si useranno le classi .NET seguenti:

* [CosmosClient](): questa classe fornisce una rappresentazione logica lato client per il servizio Azure Cosmos DB. L'oggetto client viene usato per configurare ed eseguire richieste nel servizio.

* [CreateDatabaseIfNotExistsAsync](): questo metodo crea (se non esiste) o ottiene (se esiste già) una risorsa database come operazione asincrona. 

* [CreateContainerIfNotExistsAsync](): questo metodo crea (se non esiste) o ottiene (se esiste già) un contenitore come operazione asincrona. È possibile controllare il codice di stato della risposta per determinare se il contenitore è stato appena creato (201) o se è stato restituito un contenitore esistente (200). 
* [CreateItemAsync](): questo metodo crea un elemento nel contenitore. 

* [QueryItemsAsync](): esegue una query per ottenere gli elementi necessari. Una query SQL viene passata all'interno di questo metodo. 

* [DeleteAsync](): elimina il database specificato dall'account Azure Cosmos. Il metodo `DeleteAsync` elimina solo il database. L'eliminazione dell'istanza di `Cosmosclient` deve essere eseguita separatamente, nel metodo DeleteDatabaseAndCleanupAsync. 

 ## <a id="code-examples"></a>Esempi di codice

Il codice di esempio descritto in questo articolo crea un database relativo alla famiglia in Azure Cosmos DB. Il database contiene i dettagli della famiglia, ad esempio nome, indirizzo, località e genitori, figli e animali domestici associati. Prima di popolare i dati nell'account Azure Cosmos, definire le proprietà di un elemento della famiglia. Creare una nuova classe denominata `Family.cs` al livello radice dell'applicazione di esempio e aggiungervi il codice seguente:

```csharp
using Newtonsoft.Json;

namespace todo
{
    public class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
        public override string ToString()
        {
            return JsonConvert.SerializeObject(this);
        }
    }

    public class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    public class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    public class Pet
    {
        public string GivenName { get; set; }
    }

    public class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }
}
```

### <a name="add-the-using-directives--define-the-client-object"></a>Aggiungere le direttive using e definire l'oggetto client

Dalla directory del progetto aprire il file `Program.cs` nell'editor e aggiungere le direttive using seguenti all'inizio dell'applicazione:

```csharp

using System;
using System.Threading.Tasks;
using System.Configuration;
using System.Collections.Generic;
using System.Net;
using Microsoft.Azure.Cosmos;
```

Nel `program.cs file` aggiungere il codice per leggere le variabili di ambiente impostate nel passaggio precedente. Definire gli oggetti `CosmosClient`, `Database` e `Container`. Aggiungere quindi il codice al metodo principale che chiama il metodo `GetStartedDemoAsync` in cui si gestiscono le risorse dell'account Azure Cosmos. 

```csharp
namespace todo
{
public class Program
{

    /// The Azure Cosmos DB endpoint for running this GetStarted sample.
    private string EndpointUrl = Environment.GetEnvironmentVariable("EndpointUrl");

    /// The primary key for the Azure DocumentDB account.
    private string PrimaryKey = Environment.GetEnvironmentVariable("PrimaryKey");

    // The Cosmos client instance
    private CosmosClient cosmosClient;

    // The database we will create
    private Database database;

    // The container we will create.
    private Container container;

    // The name of the database and container we will create
    private string databaseId = "FamilyDatabase";
    private string containerId = "FamilyContainer";

    public static async Task Main(string[] args)
    {
       try
       {
           Console.WriteLine("Beginning operations...\n");
           Program p = new Program();
           await p.GetStartedDemoAsync();

        }
        catch (CosmosException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine("{0} error occurred: {1}", de.StatusCode, de);
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: {0}", e);
        }
        finally
        {
            Console.WriteLine("End of demo, press any key to exit.");
            Console.ReadKey();
        }
    }
}
}
```

### <a name="create-a-database"></a>Creare un database 

Definire il metodo `CreateDatabaseAsync` nella classe `program.cs`. Questo metodo crea `FamilyDatabase` se non esiste già. 

```csharp
private async Task CreateDatabaseAsync()
{
    // Create a new database
    this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
    Console.WriteLine("Created Database: {0}\n", this.database.Id);
}
```

### <a name="create-a-container"></a>Creare un contenitore

Definire il metodo `CreateContainerAsync` nella classe `program.cs`. Questo metodo crea `FamilyContainer` se non esiste già. 

```csharp
/// Create the container if it does not exist. 
/// Specifiy "/LastName" as the partition key since we're storing family information, to ensure good distribution of requests and storage.
private async Task CreateContainerAsync()
{
    // Create a new container
    this.container = await this.database.CreateContainerIfNotExistsAsync(containerId, "/LastName");
    Console.WriteLine("Created Container: {0}\n", this.container.Id);
}
```

### <a name="create-an-item"></a>Creare un elemento

Creare un elemento della famiglia aggiungendo il metodo `AddItemsToContainerAsync` con il codice seguente:

```csharp
private async Task AddItemsToContainerAsync()
{
    // Create a family object for the Andersen family
    Family andersenFamily = new Family
    {
        Id = "Andersen.1",
        LastName = "Andersen",
        Parents = new Parent[]
        {
           new Parent { FirstName = "Thomas" },
           new Parent { FirstName = "Mary Kay" }
        },
        Children = new Child[]
        {
           new Child
            {
                FirstName = "Henriette Thaulow",
                Gender = "female",
                Grade = 5,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Fluffy" }
                }
            }
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = false
 };

try
{
    // Read the item to see if it exists. ReadItemAsync will throw an exception if the item does not exist and return status code 404 (Not found).
    ItemResponse<Family> andersenFamilyResponse = await this.container.ReadItemAsync<Family>(andersenFamily.Id, new PartitionKey(andersenFamily.LastName));
    Console.WriteLine("Item in database with id: {0} already exists\n", andersenFamilyResponse.Resource.Id);
}
catch(CosmosException ex) when (ex.StatusCode == HttpStatusCode.NotFound)
{
    // Create an item in the container representing the Andersen family. Note we provide the value of the partition key for this item, which is "Andersen"
    ItemResponse<Family> andersenFamilyResponse = await this.container.CreateItemAsync<Family>(andersenFamily, new PartitionKey(andersenFamily.LastName));

    // Note that after creating the item, we can access the body of the item with the Resource property off the ItemResponse. We can also access the RequestCharge property to see the amount of RUs consumed on this request.
    Console.WriteLine("Created item in database with id: {0} Operation consumed {1} RUs.\n", andersenFamilyResponse.Resource.Id, andersenFamilyResponse.RequestCharge);
}

```

### <a name="query-the-items"></a>Eseguire una query sugli elementi

Dopo aver inserito un elemento, è possibile eseguire una query per ottenere i dettagli della famiglia "Andersen". Il codice seguente illustra come eseguire la query usando direttamente la query SQL. La query SQL per ottenere i dettagli della famiglia "Anderson" è: `SELECT * FROM c WHERE c.LastName = 'Andersen'`. Definire il metodo `QueryItemsAsync` nella classe `program.cs` e aggiungervi il codice seguente:


```csharp
private async Task QueryItemsAsync()
{
    var sqlQueryText = "SELECT * FROM c WHERE c.LastName = 'Andersen'";

    Console.WriteLine("Running query: {0}\n", sqlQueryText);

    QueryDefinition queryDefinition = new QueryDefinition(sqlQueryText);
    FeedIterator<Family> queryResultSetIterator = this.container.GetItemQueryIterator<Family>(queryDefinition);

    List<Family> families = new List<Family>();

    while (queryResultSetIterator.HasMoreResults)
    {
        FeedResponse<Family> currentResultSet = await queryResultSetIterator.ReadNextAsync();
        foreach (Family family in currentResultSet)
        {
            families.Add(family);
            Console.WriteLine("\tRead {0}\n", family);
        }
    }
}

```

### <a name="delete-the-database"></a>eliminare il database 

È infine possibile eliminare il database aggiungendo il metodo `DeleteDatabaseAndCleanupAsync` con il codice seguente:

```csharp
private async Task DeleteDatabaseAndCleanupAsync()
{
   DatabaseResponse databaseResourceResponse = await this.database.DeleteAsync();
   // Also valid: await this.cosmosClient.Databases["FamilyDatabase"].DeleteAsync();

   Console.WriteLine("Deleted Database: {0}\n", this.databaseId);

   //Dispose of CosmosClient
    this.cosmosClient.Dispose();
}
```

### <a name="execute-the-crud-operations"></a>Eseguire le operazioni CRUD

Dopo aver definito tutti i metodi necessari, eseguirli con il metodo `GetStartedDemoAsync`. Il metodo `DeleteDatabaseAndCleanupAsync` è impostato come commento in questo codice perché nessuna risorsa viene visualizzata se si esegue tale metodo. È possibile rimuovere il commento dopo aver verificato che le risorse di Azure Cosmos DB siano state create nel portale di Azure. 

```csharp
public async Task GetStartedDemoAsync()
{
    // Create a new instance of the Cosmos Client
    this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    await this.CreateDatabaseAsync();
    await this.CreateContainerAsync();
    await this.AddItemsToContainerAsync();
    await this.QueryItemsAsync();
    await this.ReplaceFamilyItemAsync();
    await this.DeleteFamilyItemAsync();
    //await this.DeleteDatabaseAndCleanupAsync();
}
```

Dopo aver aggiunto tutti i metodi necessari, salvare il file `Program.cs`. 

## <a name="run-the-code"></a>Eseguire il codice

Successivamente, compilare ed eseguire l'applicazione per creare le risorse di Azure Cosmos DB. Assicurarsi di aprire una nuova finestra del prompt dei comandi. Non usare la stessa istanza usata per impostare le variabili di ambiente perché le variabili di ambiente non sono impostate nella finestra aperta corrente. Per visualizzare gli aggiornamenti, sarà necessario aprire un nuovo prompt dei comandi. 

```console
dotnet build
```

```console
dotnet run
```

Quando si esegue l'applicazione, viene generato l'output seguente. È anche possibile accedere al portale di Azure e verificare che le risorse siano state create:

```console
Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.62 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

End of demo, press any key to exit.
```

È possibile verificare che i dati vengano creati accedendo al portale di Azure e visualizzando gli elementi necessari nell'account Azure Cosmos. 

## <a name="clean-up-resources"></a>Pulire le risorse

Quando non sono più necessari, è possibile usare l'interfaccia della riga di comando di Azure o Azure PowerShell per rimuovere l'account Azure Cosmos e il gruppo di risorse corrispondente. Il comando seguente mostra come eliminare il gruppo di risorse usando l'interfaccia della riga di comando di Azure:

```azurecli
az group delete -g "myResourceGroup" -l "southcentralus"
```

## <a name="next-steps"></a>Passaggi successivi

In questo argomento di avvio rapido si è appreso come creare un account Azure Cosmos e come creare un database e un contenitore con un'app .NET Core. È ora possibile importare dati aggiuntivi nell'account Azure Cosmos con le istruzioni incluse nell'articolo seguente. 

> [!div class="nextstepaction"]
> [Importare dati in Azure Cosmos DB](import-data.md)
