---
title: Compilare un'app console .NET per gestire i dati in un account API SQL di Azure Cosmos DB
description: Informazioni su come creare risorse dell'API SQL di Azure Cosmos DB usando un'applicazione console C#.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 09/24/2019
ms.author: kirankk
ms.openlocfilehash: 35e92ff1591bc5f0427dabbf68e697d9c3c32b48
ms.sourcegitcommit: 29880cf2e4ba9e441f7334c67c7e6a994df21cfe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/26/2019
ms.locfileid: "71299278"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Compilare un'app console .NET per gestire i dati in un account API SQL di Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Questa è un'esercitazione introduttiva per l'API SQL di Azure Cosmos DB. Dopo aver seguito questa esercitazione, si otterrà un'applicazione console che consente di creare e ridefinire le query delle risorse Azure Cosmos DB.

Questa esercitazione usa la versione 3.0 o successiva di [Azure Cosmos DB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos). È possibile usare [.NET Framework o .NET Core](https://dotnet.microsoft.com/download).

Contenuto dell'esercitazione:

> [!div class="checklist"]
>
> * Creazione e connessione a un account Azure Cosmos
> * Configurazione del progetto in Visual Studio
> * Creazione di un database e di un contenitore
> * Aggiunta di elementi al contenitore
> * Esecuzione di query sul contenitore
> * Esecuzione di operazioni di creazione, lettura, aggiornamento ed eliminazione dell'elemento
> * Eliminazione del database

Non si ha tempo? Nessun problema. La soluzione completa è disponibile in [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Per istruzioni rapide, vedere la sezione [Ottenere la soluzione completa per l'esercitazione](#GetSolution).

Ecco come procedere.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passaggio 1: Creare un account Azure Cosmos DB

Creare prima di tutto un account Azure Cosmos DB. Se si ha già un account che si vuole usare, ignorare questa sezione. Se si usa l'emulatore di Azure Cosmos DB, seguire la procedura illustrata in [Emulatore di Azure Cosmos DB](local-emulator.md) per configurarlo. Quindi procedere con il [Passaggio 2: Configurare il progetto di Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Passaggio 2: Configurare il progetto di Visual Studio

1. Aprire Visual Studio e selezionare **Crea un nuovo progetto**.
1. In **Crea un nuovo progetto** scegliere **App console (.NET Framework)** per C#, quindi selezionare **Avanti**.
1. Assegnare al progetto il nome *CosmosGettingStartedTutorial* e quindi selezionare **Crea**.

    ![Configurare il progetto](./media/sql-api-get-started/configure-cosmos-getting-started-2019.png)

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla nuova applicazione console, disponibile nella soluzione di Visual Studio, quindi scegliere **Gestisci pacchetti NuGet**.
1. In **Gestione pacchetti NuGet** selezionare **Sfoglia** e cercare *Microsoft.Azure.Cosmos*. Scegliere **Microsoft.Azure.Cosmos** e selezionare **Installa**.

   ![Installare NuGet per l'SDK client di Azure Cosmos DB](./media/sql-api-get-started/cosmos-getting-started-manage-nuget-2019.png)

   L'ID pacchetto per la libreria client dell'API SQL di Azure Cosmos DB è [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).

L'installazione è riuscita. Ora che abbiamo completato l'installazione, iniziamo a scrivere il codice. Per il progetto completo di questa esercitazione, vedere [Sviluppo di un'app console .NET tramite Azure Cosmos DB](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a id="Connect"></a>Passaggio 3: collegarsi a un account di Azure Cosmos DB

1. Sostituire i riferimenti all'inizio dell'applicazione C#, nel file *Program.cs*, con questi riferimenti:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Aggiungere queste costanti e variabili nella classe `Program`.

    ```csharp
    public class Program
    {
        // ADD THIS PART TO YOUR CODE

        // The Azure Cosmos DB endpoint for running this sample.
        private static readonly string EndpointUri = "<your endpoint here>";
        // The primary key for the Azure Cosmos account.
        private static readonly string PrimaryKey = "<your primary key>";

        // The Cosmos client instance
        private CosmosClient cosmosClient;

        // The database we will create
        private Database database;

        // The container we will create.
        private Container container;

        // The name of the database and container we will create
        private string databaseId = "FamilyDatabase";
        private string containerId = "FamilyContainer";
    }
    ```

   > [!NOTE]
   > Se si ha familiarità con la versione precedente di .NET SDK, i termini *raccolta* e *documento* saranno già noti. Poiché Azure Cosmos DB supporta più modelli di API, nella versione 3.0 di .NET SDK vengono usati i termini generici *contenitore* ed *elemento*. Un *contenitore* può essere una raccolta, un grafo o una tabella. Un *elemento* può essere un documento, un arco/vertice o una riga ed è il contenuto all'interno di un contenitore. Per altre informazioni, vedere [Uso di contenitori ed elementi di Azure Cosmos](databases-containers-items.md).

1. Aprire il [portale di Azure](https://portal.azure.com). Trovare l'account Azure Cosmos DB e quindi selezionare **Chiavi**.

   ![Ottenere le chiavi di Azure Cosmos DB dal portale di Azure](./media/sql-api-get-started/cosmos-getting-started-portal-keys.png)

1. In *Program.cs* sostituire `<your endpoint URL>` con il valore di **URI**. Sostituire `<your primary key>` con il valore di **CHIAVE PRIMARIA**.

1. Sotto il metodo **Main** aggiungere una nuova attività asincrona denominata **GetStartedDemoAsync**, che creerà un'istanza del nuovo `CosmosClient`.

    ```csharp
    public static async Task Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    /*
        Entry point to call methods that operate on Azure Cosmos DB resources in this sample
    */
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
    }
    ```

    Si usa **GetStartedDemoAsync** come punto di ingresso che chiama i metodi che agiscono sulle risorse di Azure Cosmos DB.

1. Aggiungere il codice seguente per eseguire l'attività asincrona **GetStartedDemoAsync** dal metodo **Main**. Il metodo **Main** rileva le eccezioni e le scrive nella console.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Premere F5 per eseguire l'applicazione.

    La console visualizza il messaggio: **End of demo, press any key to exit** (Fine della demo, premere un tasto qualsiasi per uscire). Questo messaggio conferma che l'applicazione ha stabilito una connessione con Azure Cosmos DB. È quindi possibile chiudere la finestra della console.

Congratulazioni! La connessione con un account di Azure Cosmos DB è stata stabilita correttamente.

## <a name="step-4-create-a-database"></a>Passaggio 4: Creare un database

Un database è un contenitore logico di elementi partizionati tra contenitori. Il metodo `CreateDatabaseIfNotExistsAsync` o `CreateDatabaseAsync` della classe [CosmosClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) può creare un database.

1. Copiare e incollare il metodo `CreateDatabaseAsync` seguente sotto il metodo `GetStartedDemoAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

    `CreateDatabaseAsync` crea un nuovo database con ID `FamilyDatabase`, se non esiste già, ossia l'ID specificato nel campo `databaseId`.

1. Copiare e incollare il codice seguente nel punto in cui è stata creata l'istanza di CosmosClient per chiamare il metodo **CreateDatabaseAsync** appena aggiunto.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);

        //ADD THIS PART TO YOUR CODE
        await this.CreateDatabaseAsync();
    }
    ```

    A questo punto il file *Program.cs* dovrebbe avere un aspetto simile al seguente, con l'endpoint e la chiave primaria compilati.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStartedTutorial
    {
        class Program
        {
            // The Azure Cosmos DB endpoint for running this sample.
            private static readonly string EndpointUri = "<your endpoint here>";
            // The primary key for the Azure Cosmos account.
            private static readonly string PrimaryKey = "<your primary key>";

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
                    Console.WriteLine("Beginning operations...");
                    Program p = new Program();
                    await p.GetStartedDemoAsync();
                }
                catch (CosmosException de)
                {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}\n", de.StatusCode, de);
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: {0}\n", e);
                }
                finally
                {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
                }
            }

            /// <summary>
            /// Entry point to call methods that operate on Azure Cosmos DB resources in this sample
            /// </summary>
            public async Task GetStartedDemoAsync()
            {
                // Create a new instance of the Cosmos Client
                this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
                await this.CreateDatabaseAsync();
            }

            /// <summary>
            /// Create the database if it does not exist
            /// </summary>
            private async Task CreateDatabaseAsync()
            {
                // Create a new database
                this.database = await this.cosmosClient.CreateDatabaseIfNotExistsAsync(databaseId);
                Console.WriteLine("Created Database: {0}\n", this.database.Id);
            }
        }
    }
    ```

1. Premere F5 per eseguire l'applicazione.

Congratulazioni! La creazione di un database Azure Cosmos è stata completata.  

## <a id="CreateColl"></a>Passaggio 5: Creare un contenitore

> [!WARNING]
> Il metodo `CreateContainerIfNotExistsAsync` crea un nuovo contenitore, che presenta implicazioni per i prezzi. Per altre informazioni, visitare la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Un contenitore può essere creato usando il metodo [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) o [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) della classe `CosmosDatabase`. Un contenitore è costituito da elementi (documenti JSON nel caso dell'API SQL) e dalla logica dell'applicazione lato server associata in JavaScript, ad esempio stored procedure, funzioni definite dall'utente e trigger.

1. Copiare e incollare il metodo `CreateContainerAsync` seguente sotto il metodo `CreateDatabaseAsync`. `CreateContainerAsync` crea un nuovo contenitore con l'ID `FamilyContainer`, se non esiste già, usando l'ID specificato nel campo `containerId` partizionato dalla proprietà `LastName`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateContainerAsync&highlight=9)]

1. Copiare e incollare il codice seguente nel punto in cui è stata creata l'istanza di CosmosClient per chiamare il metodo **CreateContainer** appena aggiunto.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();

        //ADD THIS PART TO YOUR CODE
        await this.CreateContainerAsync();
    }
    ```

1. Premere F5 per eseguire l'applicazione.

Congratulazioni! La creazione di un contenitore Azure Cosmos è stata completata.  

## <a id="CreateDoc"></a>Passaggio 6: Aggiungere elementi al contenitore

Il metodo [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) della classe `CosmosContainer` può creare un elemento. Quando si usa l'API SQL, gli elementi vengono proiettati come documenti, che sono contenuti JSON arbitrari definiti dall'utente. È ora possibile inserire un elemento nel contenitore Azure Cosmos.

Prima di tutto creare una classe `Family` che rappresenterà gli oggetti archiviati in Azure Cosmos DB in questo esempio. Creare inoltre le sottoclassi `Parent`, `Child`, `Pet`, `Address` usate all'interno di `Family`. L'elemento deve avere una proprietà `Id` serializzata come `id` in JSON.

1. Premere CTRL+MAIUSC+A per aprire la finestra di dialogo **Aggiungi nuovo elemento**. Aggiungere una nuova classe `Family.cs` al progetto.

    ![Screenshot dell'aggiunta di una nuova classe Family.cs nel progetto](./media/sql-api-get-started/cosmos-getting-started-add-family-class-2019.png)

1. Copiare e incollare le classi `Family`, `Parent`, `Child`, `Pet` e `Address` in `Family.cs`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Nel file *Program.cs* aggiungere il metodo `AddItemsToContainerAsync` dopo il metodo `CreateContainerAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

    Il codice verifica che non esista già un elemento con lo stesso ID. Verranno inseriti due elementi, uno per *Andersen Family* e l'altro per *Wakefield Family*.

1. Aggiungere una chiamata a `AddItemsToContainerAsync` nel metodo `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.AddItemsToContainerAsync();
    }
    ```

1. Premere F5 per eseguire l'applicazione.

Congratulazioni! La creazione di due elementi Azure Cosmos è stata completata.  

## <a id="Query"></a>Passaggio 7: eseguire query sulle risorse di Azure Cosmos DB

Azure Cosmos DB supporta le query complesse sui documenti JSON archiviati in ogni contenitore. Per altre informazioni, vedere [Introduzione alle query SQL](sql-api-sql-query.md). Il codice di esempio seguente mostra come eseguire una query sugli elementi inseriti nella procedura precedente.

1. Copiare e incollare il metodo `QueryItemsAsync` dopo il metodo `AddItemsToContainerAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=QueryItemsAsync&highlight=10-11,17-18)]

1. Aggiungere una chiamata a ``QueryItemsAsync`` nel metodo ``GetStartedDemoAsync``.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();

        //ADD THIS PART TO YOUR CODE
        await this.QueryItemsAsync();
    }
    ```

1. Premere F5 per eseguire l'applicazione.

Congratulazioni! Sono state eseguite query su un contenitore Azure Cosmos.

## <a id="ReplaceItem"></a>Passaggio 8: sostituire un elemento JSON

Aggiornare ora un elemento in Azure Cosmos DB. Cambiare la proprietà `IsRegistered` di `Family` e l'oggetto `Grade` di uno dei figli.

1. Copiare e incollare il metodo `ReplaceFamilyItemAsync` dopo il metodo `QueryItemsAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Aggiungere una chiamata a `ReplaceFamilyItemAsync` nel metodo `GetStartedDemoAsync`.

    ```csharp
    public async Task GetStartedDemoAsync()
    {
        // Create a new instance of the Cosmos Client
        this.cosmosClient = new CosmosClient(EndpointUri, PrimaryKey);
        await this.CreateDatabaseAsync();
        await this.CreateContainerAsync();
        await this.AddItemsToContainerAsync();
        await this.QueryItemsAsync();

        //ADD THIS PART TO YOUR CODE
        await this.ReplaceFamilyItemAsync();
    }
    ```

1. Premere F5 per eseguire l'applicazione.

Congratulazioni! La sostituzione di un elemento Azure Cosmos è stata completata.

## <a id="DeleteDocument"></a>Passaggio 9: Elimina elemento

Eliminare ora un elemento in Azure Cosmos DB.

1. Copiare e incollare il metodo `DeleteFamilyItemAsync` dopo il metodo `ReplaceFamilyItemAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Aggiungere una chiamata a `DeleteFamilyItemAsync` nel metodo `GetStartedDemoAsync`.

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

        //ADD THIS PART TO YOUR CODE
        await this.DeleteFamilyItemAsync();
    }
    ```

1. Premere F5 per eseguire l'applicazione.

Congratulazioni! L'eliminazione di un elemento Azure Cosmos è stata completata.

## <a id="DeleteDatabase"></a>Passaggio 10: eliminare il database

A questo punto verrà eliminato il database. Se si elimina il database creato, vengono rimosse tutte le risorse figlio, come contenitori, elementi ed eventuali stored procedure, funzioni definite dall'utente e trigger. Verrà anche eliminata l'istanza di `CosmosClient`.

1. Copiare e incollare il metodo `DeleteDatabaseAndCleanupAsync` dopo il metodo `DeleteFamilyItemAsync`.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Aggiungere una chiamata a ``DeleteDatabaseAndCleanupAsync`` nel metodo ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

1. Premere F5 per eseguire l'applicazione.

Congratulazioni! L'eliminazione di un database Azure Cosmos è stata completata.

## <a id="Run"></a>Passaggio 11: eseguire l'intera applicazione console C#

Premere F5 in Visual Studio per compilare ed eseguire l'applicazione in modalità di debug.

Dovrebbe essere visualizzato l'output dell'intera app in una finestra della console. L'output mostra i risultati delle query aggiunte. Dovrebbe essere analogo al testo di esempio seguente.

```cmd
Beginning operations...

Created Database: FamilyDatabase

Created Container: FamilyContainer

Created item in database with id: Andersen.1 Operation consumed 11.43 RUs.

Created item in database with id: Wakefield.7 Operation consumed 14.29 RUs.

Running query: SELECT * FROM c WHERE c.LastName = 'Andersen'

        Read {"id":"Andersen.1","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":false}

Updated Family [Wakefield,Wakefield.7].
        Body is now: {"id":"Wakefield.7","LastName":"Wakefield","Parents":[{"FamilyName":"Wakefield","FirstName":"Robin"},{"FamilyName":"Miller","FirstName":"Ben"}],"Children":[{"FamilyName":"Merriam","FirstName":"Jesse","Gender":"female","Grade":6,"Pets":[{"GivenName":"Goofy"},{"GivenName":"Shadow"}]},{"FamilyName":"Miller","FirstName":"Lisa","Gender":"female","Grade":1,"Pets":null}],"Address":{"State":"NY","County":"Manhattan","City":"NY"},"IsRegistered":true}

Deleted Family [Wakefield,Wakefield.7]

Deleted Database: FamilyDatabase

End of demo, press any key to exit.
```

Congratulazioni! L'esercitazione è stata completata ed è stata creata un'applicazione console C# funzionante.

## <a id="GetSolution"></a> Ottenere la soluzione completa per l'esercitazione

Se non si ha tempo per completare le procedure dell'esercitazione o se si vogliono solo ottenere gli esempi di codice, è possibile scaricarli.

Per creare la soluzione `GetStarted`, è necessario soddisfare i prerequisiti seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Un [account Azure Cosmos DB][cosmos-db-create-account].
* La soluzione [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) disponibile su GitHub.

Per ripristinare i riferimenti ad Azure Cosmos DB .NET SDK in Visual Studio, fare clic con il pulsante destro del mouse sulla soluzione in **Esplora soluzioni** e quindi scegliere **Ripristina pacchetti NuGet**. Quindi, nel file *App.config* aggiornare i valori di `EndPointUri` e `PrimaryKey` come descritto in [Passaggio 3: Connettersi a un account Azure Cosmos DB](#Connect).

A questo punto non resta che procedere con la compilazione.

## <a name="next-steps"></a>Passaggi successivi

* Per un'esercitazione su MVC ASP.NET più complessa, Per istruzioni dettagliate, vedere [Esercitazione: Sviluppare un'applicazione Web MVC ASP.NET Core con Azure Cosmos DB usando .NET SDK](sql-api-dotnet-application.md).
* Per informazioni sull'esecuzione di test delle prestazioni e della scalabilità con Azure Cosmos DB, vedere [Test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md).
* Per informazioni su come monitorare le richieste, l'utilizzo e le risorse di archiviazione di Azure Cosmos DB, vedere [Monitorare le metriche di prestazioni e archiviazione in Azure Cosmos DB](monitor-accounts.md).
* Per eseguire query sul set di dati di esempio, vedere [Playground per le query](https://www.documentdb.com/sql/demo).
* Per altre informazioni su Azure Cosmos DB, vedere [Introduzione ad Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
