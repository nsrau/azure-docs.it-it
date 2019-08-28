---
title: Compilare un'app console .NET per gestire i dati in un account API SQL di Azure Cosmos DB
description: Informazioni su come creare risorse dell'API SQL di Azure Cosmos DB usando un'applicazione console C#.
author: kirankumarkolli
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: kirankk
ms.openlocfilehash: ea3779ce7d4d77e6b0a55b8a00374b701a073b66
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876432"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Compilare un'app console .NET per gestire i dati in un account API SQL di Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
>

Questa è un'esercitazione introduttiva per l'API SQL di Azure Cosmos DB. Dopo aver seguito questa esercitazione, si otterrà un'applicazione console che consente di creare e ridefinire le query delle risorse Azure Cosmos DB. Questa esercitazione usa la [versione 3.0+](https://www.nuget.org/packages/Microsoft.Azure.Cosmos) di Azure Cosmos DB .NET SDK, che può essere destinata a [.NET Standard](https://dotnet.microsoft.com/download) o a [.NET Core](https://dotnet.microsoft.com/download).

Contenuto dell'esercitazione:

> [!div class="checklist"]
> * Creazione e connessione a un account Azure Cosmos
> * Configurazione del progetto in Visual Studio
> * Creazione di un database e di un contenitore
> * Aggiunta di elementi al contenitore
> * Esecuzione di query sul contenitore
> * Esecuzione di operazioni CRUD sull'elemento
> * Eliminazione del database

Non si ha tempo? Nessun problema. La soluzione completa è disponibile in [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). Per istruzioni rapide, vedere la sezione [Ottenere la soluzione completa per l'esercitazione](#GetSolution).

Ecco come procedere.

## <a name="prerequisites"></a>Prerequisiti

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/).

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passaggio 1: Creare un account Azure Cosmos DB
Creare prima di tutto un account Azure Cosmos DB. Se si ha già un account, è possibile ignorare questo passaggio e passare a [Configurare la soluzione Visual Studio](#SetupVS). Se si usa l'emulatore di Azure Cosmos DB, seguire i passaggi descritti nell'articolo [Usare l'emulatore di Azure Cosmos DB per sviluppo e test locali](local-emulator.md) per configurare l'emulatore e proseguire con il passaggio [Configurare il progetto di Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount-preview](../../includes/cosmos-db-create-dbaccount-preview.md)]

## <a id="SetupVS"></a>Passaggio 2: configurare il progetto di Visual Studio
1. Aprire **Visual Studio 2017** nel computer.
1. Scegliere **Nuovo** dal menu **File** e quindi selezionare **Progetto**.
1. Nella finestra di dialogo **Nuovo progetto** selezionare **Visual C#**  / **App console (.NET Framework)** , assegnare un nome al progetto e quindi fare clic su **OK**.
    ![Screenshot della finestra Nuovo progetto](./media/sql-api-get-started/dotnet-tutorial-visual-studio-new-project.png)

    > [!NOTE]
    > Per la destinazione .NET Core, nella finestra di dialogo **Nuovo progetto** selezionare **Visual C#**  / **App console (.NET Core)** , assegnare un nome al progetto e quindi fare clic su **OK**

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla nuova applicazione console, disponibile nella soluzione di Visual Studio, quindi scegliere **Gestisci pacchetti NuGet**.

    ![Screenshot del menu selezionato con il pulsante destro del mouse per il progetto](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget.png)
1. Nella scheda **NuGet** fare clic su **Sfoglia** e digitare **Microsoft.Azure.Cosmos** nella casella di ricerca.
1. Nei risultati trovare **Microsoft.Azure.Cosmos** e fare clic su **Installa**.
   L'ID pacchetto per la libreria client dell'API SQL di Azure Cosmos DB è [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/).
   ![Screenshot del menu di NuGet per l'individuazione di Azure Cosmos DB Client SDK](./media/sql-api-get-started/dotnet-tutorial-visual-studio-manage-nuget-2.png)

    Se viene visualizzato un messaggio sulla verifica delle modifiche alla soluzione, fare clic su **OK**. Se viene visualizzato un messaggio sull'accettazione della licenza, fare clic su **Accetto**.

L'installazione è riuscita. Ora che abbiamo completato l'installazione, iniziamo a scrivere il codice. Un progetto di codice completo di questa esercitazione è disponibile in [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started).

## <a id="Connect"></a>Passaggio 3: collegarsi a un account di Azure Cosmos DB
1. Prima di tutto sostituire i riferimenti all'inizio dell'applicazione C#, nel file **Program.cs**, con questi riferimenti:

   ```csharp
   using System;
   using System.Threading.Tasks;
   using System.Configuration;
   using System.Collections.Generic;
   using System.Net;
   using Microsoft.Azure.Cosmos;
   ```

1. Aggiungere quindi queste costanti e variabili nella classe pubblica ``Program``.

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

    Si noti che, se si ha familiarità con la versione precedente di .NET SDK, ci si potrebbe aspettare di vedere termini come "raccolta" e "documento". Poiché Azure Cosmos DB supporta più modelli di API, nella versione 3.0+ di .NET SDK vengono usati i termini generici "contenitore" ed "elemento". Un contenitore può essere una raccolta, un grafo o una tabella. Un elemento può essere un documento, un arco/vertice o una riga ed è il contenuto all'interno di un contenitore. [Altre informazioni su database, contenitori ed elementi.](databases-containers-items.md)

1. Recuperare la chiave primaria e l'URL dell'endpoint dal [portale di Azure](https://portal.azure.com).

    Nel portale di Azure passare all'account Azure Cosmos DB e quindi fare clic su **Chiavi**.

    Copiare l'URI dal portale e incollarlo in `<your endpoint URL>` nel file ```Program.cs```. Copiare la CHIAVE PRIMARIA dal portale e incollarla in `<your primary key>`.

   ![Screenshot per il recupero delle chiavi di Azure Cosmos DB dal portale di Azure](./media/sql-api-get-started/dotnet-tutorial-portal-keys.png)

1. A questo punto occorre creare una nuova istanza di ```CosmosClient``` e configurare lo scaffolding per il programma.

    Sotto il metodo **Main** aggiungere una nuova attività asincrona denominata **GetStartedDemoAsync**, che creerà un'istanza del nuovo ```CosmosClient```. Si userà **GetStartedDemoAsync** come punto di ingresso che chiama i metodi che operano sulle risorse di Azure Cosmos DB.

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

1. Aggiungere il codice seguente per eseguire l'attività asincrona **GetStartedDemoAsync** dal metodo **Main**. Il metodo **Main** rileva le eccezioni e le scrive nella console.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=Main)]

1. Premere **F5** per eseguire l'applicazione. L'output della finestra della console mostra il messaggio `End of demo, press any key to exit.`, che conferma che è stata stabilita la connessione ad Azure Cosmos DB. È quindi possibile chiudere la finestra della console.

Congratulazioni! La connessione a un account di Azure Cosmos DB è stata stabilita correttamente. 

## <a name="step-4-create-a-database"></a>Passaggio 4: Creare un database
Un database si può creare usando la funzione [**CreateDatabaseIfNotExistsAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) o [**CreateDatabaseAsync**](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmos.cosmosclient) della classe ``CosmosClient``. Un database è un contenitore logico di elementi partizionati tra contenitori.

1. Copiare e incollare il metodo **CreateDatabaseAsync** sotto il metodo **GetStartedDemoAsync**. **CreateDatabaseAsync** creerà un nuovo database, se non esiste già, con l'ID ``FamilyDatabase`` specificato nel campo ``databaseId``. 

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=CreateDatabaseAsync&highlight=7)]

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

    A questo punto il codice dovrebbe avere un aspetto simile al seguente, con l'endpoint e la chiave primaria compilati.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using System.Configuration;
    using System.Collections.Generic;
    using System.Net;
    using Microsoft.Azure.Cosmos;

    namespace CosmosGettingStarted
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

Premere **F5** per eseguire l'applicazione.

Congratulazioni! La creazione di un database Azure Cosmos è stata completata.  

## <a id="CreateColl"></a>Passaggio 5: Creare un contenitore
> [!WARNING]
> La chiamata del metodo **CreateContainerIfNotExistsAsync** creerà un nuovo contenitore, con implicazioni in termini di prezzi. Per altre informazioni, visitare la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/).
>
>

Un contenitore può essere creato usando la funzione [**CreateContainerIfNotExistsAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerifnotexistsasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerIfNotExistsAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) o [**CreateContainerAsync**](/dotnet/api/microsoft.azure.cosmos.database.createcontainerasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Database_CreateContainerAsync_Microsoft_Azure_Cosmos_ContainerProperties_System_Nullable_System_Int32__Microsoft_Azure_Cosmos_RequestOptions_System_Threading_CancellationToken_) della classe **CosmosDatabase**. Un contenitore è costituito da elementi (documenti JSON nel caso dell'API SQL) e dalla logica dell'applicazione lato server associata in JavaScript, ad esempio stored procedure, funzioni definite dall'utente e trigger.

1. Copiare e incollare il metodo **CreateContainerAsync** sotto il metodo **CreateDatabaseAsync**. **CreateContainerAsync** creerà un nuovo contenitore, se non esiste già, con l'ID ``FamilyContainer`` specificato nel campo ``containerId`` partizionato dalla proprietà ``LastName``.

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

   Premere **F5** per eseguire l'applicazione.

Congratulazioni! La creazione di un contenitore Azure Cosmos è stata completata.  

## <a id="CreateDoc"></a>Passaggio 6: Aggiungere elementi al contenitore
È possibile creare un elemento usando la funzione [**CreateItemAsync**](/dotnet/api/microsoft.azure.cosmos.container.createitemasync?view=azure-dotnet#Microsoft_Azure_Cosmos_Container_CreateItemAsync__1___0_System_Nullable_Microsoft_Azure_Cosmos_PartitionKey__Microsoft_Azure_Cosmos_ItemRequestOptions_System_Threading_CancellationToken_) della classe **CosmosContainer**. Quando si usa l'API SQL, gli elementi vengono proiettati come documenti, che sono contenuto JSON definito dall'utente (arbitrario). È ora possibile inserire un elemento nel contenitore Azure Cosmos.

Prima di tutto verrà creata una classe **Family** che rappresenterà gli oggetti archiviati in Azure Cosmos DB in questo esempio. Verranno create anche le sottoclassi **Parent**, **Child**, **Pet** e **Address** da usare in **Family**. Si noti che l'elemento deve avere una proprietà **Id** serializzata come **id** in JSON.

1. Premere **CTRL+MAIUSC+A** per aprire la finestra di dialogo **Aggiungi nuovo elemento**. Aggiungere una nuova classe **Family.cs** al progetto.

    ![Screenshot dell'aggiunta di una nuova classe Family.cs nel progetto](./media/sql-api-get-started/dotnet-tutorial-visual-studio-add-family-class.png)

1. Copiare e incollare le classi **Family**, **Parent**, **Child**, **Pet** e **Address** in **Family.cs**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Family.cs)]

1. Tornare a **Program.cs** e aggiungere il metodo **AddItemsToContainerAsync** sotto il metodo **CreateContainerAsync**.
Il codice verifica che non esista già un elemento con lo stesso ID prima di crearlo. Si inseriranno due elementi, relativi rispettivamente alla famiglia Andersen e alla famiglia Wakefield.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=AddItemsToContainerAsync)]

1. Aggiungere una chiamata a ``AddItemsToContainerAsync`` nel metodo ``GetStartedDemoAsync``.

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

Premere **F5** per eseguire l'applicazione.

Congratulazioni! La creazione di due elementi di Azure Cosmos è stata completata.  

## <a id="Query"></a>Passaggio 7: eseguire query sulle risorse di Azure Cosmos DB
Azure Cosmos DB supporta le [query](sql-api-sql-query.md) complesse sui documenti JSON archiviati in ogni contenitore. Il codice di esempio seguente mostra come eseguire una query sugli elementi inseriti nella procedura precedente.

1. Copiare e incollare il metodo **QueryItemsAsync** sotto il metodo **AddItemsToContainerAsync**.

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

Premere **F5** per eseguire l'applicazione.

Congratulazioni! L'esecuzione di una query su un contenitore Azure Cosmos è stata completata.

## <a id="ReplaceItem"></a>Passaggio 8: sostituire un elemento JSON
In questo passaggio si aggiornerà un elemento in Azure Cosmos DB.

1. Copiare e incollare il metodo **ReplaceFamilyItemAsync** sotto il metodo **QueryItemsAsync**. Si cambierà la proprietà ``IsRegistered`` della famiglia e il ``Grade`` di uno dei figli.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=ReplaceFamilyItemAsync&highlight=15)]

1. Aggiungere una chiamata a ``ReplaceFamilyItemAsync`` nel metodo ``GetStartedDemoAsync``.

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

   Premere **F5** per eseguire l'applicazione.

Congratulazioni! La sostituzione di un elemento di Azure Cosmos è stata completata.

## <a id="DeleteDocument"></a>Passaggio 9: Elimina elemento
In questo passaggio si eliminerà un elemento in Azure Cosmos DB.

1. Copiare e incollare il metodo **DeleteFamilyItemAsync** sotto il metodo **ReplaceFamilyItemAsync**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteFamilyItemAsync&highlight=10)]

1. Aggiungere una chiamata a ``DeleteFamilyItemAsync`` nel metodo ``GetStartedDemoAsync``.

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

Premere **F5** per eseguire l'applicazione.

Congratulazioni! L'eliminazione di un elemento di Azure Cosmos è stata completata.

## <a id="DeleteDatabase"></a>Passaggio 10: eliminare il database
È il momento di eliminare il database. Eliminando il database creato, insieme al database vengono rimosse tutte le risorse figlio, ossia contenitori, elementi ed eventuali stored procedure, funzioni definite dall'utente e trigger. Si procederà anche all'eliminazione dell'istanza di **CosmosClient**.

1. Copiare e incollare il metodo **DeleteDatabaseAndCleanupAsync** sotto il metodo **DeleteFamilyItemAsync**.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=DeleteDatabaseAndCleanupAsync)]

1. Aggiungere una chiamata a ``DeleteDatabaseAndCleanupAsync`` nel metodo ``GetStartedDemoAsync``.

    [!code-csharp[](~/cosmos-dotnet-getting-started/CosmosGettingStartedTutorial/Program.cs?name=GetStartedDemoAsync&highlight=14)]

Premere **F5** per eseguire l'applicazione.

Congratulazioni! L'eliminazione di un database Azure Cosmos è stata completata.

## <a id="Run"></a>Passaggio 11: eseguire l'intera applicazione console C#
Premere F5 in Visual Studio per compilare ed eseguire l'applicazione in modalità di debug.

Dovrebbe essere visualizzato l'output dell'intera app in una finestra della console. L'output visualizzerà i risultati delle query aggiunte e dovrà corrispondere al testo di esempio riportato di seguito.

```
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
Se non si ha tempo per completare le procedure dell'esercitazione o se si vogliono solo scaricare gli esempi di codice, è possibile ottenerli da [GitHub](https://github.com/Azure-Samples/cosmos-dotnet-getting-started). 

Per compilare la soluzione GetStarted, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Un [account Azure Cosmos DB][cosmos-db-create-account].
* La soluzione [GetStarted](https://github.com/Azure-Samples/cosmos-dotnet-getting-started) disponibile su GitHub.

Per ripristinare i riferimenti ad Azure Cosmos DB .NET SDK in Visual Studio, fare clic con il pulsante destro del mouse sulla soluzione **GetStarted** in Esplora soluzioni e quindi scegliere **Ripristina pacchetti NuGet**. Nel file App.config aggiornare quindi i valori EndpointUrl e PrimaryKey come illustrato nell'articolo [Connettersi a un account Azure Cosmos DB](#Connect).

A questo punto non resta che procedere con la compilazione.

## <a name="next-steps"></a>Passaggi successivi
* Per un'esercitazione su MVC ASP.NET più complessa, Vedere [Esercitazione su MVC ASP.NET: sviluppo di applicazioni Web con Azure Cosmos DB](sql-api-dotnet-application-preview.md).
* Per la scalabilità e i test delle prestazioni con Azure Cosmos DB, vedere [Test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md).
* Informazioni su come [monitorare le richieste, l'utilizzo e le risorse di archiviazione di Azure Cosmos DB](monitor-accounts.md).
* Eseguire query sul set di dati di esempio illustrato nella pagina [Query Playground](https://www.documentdb.com/sql/demo).
* Per altre informazioni su Azure Cosmos DB, vedere [Introduzione ad Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/introduction).

[cosmos-db-create-account]: create-sql-api-java.md#create-a-database-account
