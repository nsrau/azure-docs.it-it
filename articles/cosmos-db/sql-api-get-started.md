---
title: Compilare un'app console .NET per gestire i dati in un account API SQL di Azure Cosmos DB
description: Esercitazione che crea un database online e un'app console in C# con l'API SQL.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: sngun
ms.openlocfilehash: 64aef17663fdc28a467172bbe8954fc06fdb7ff0
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59680392"
---
# <a name="build-a-net-console-app-to-manage-data-in-azure-cosmos-db-sql-api-account"></a>Compilare un'app console .NET per gestire i dati in un account API SQL di Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-get-started.md)
> * [.NET (Preview)](sql-api-dotnet-get-started-preview.md)
> * [.NET Core](sql-api-dotnetcore-get-started.md)
> * [.NET Core (Preview)](sql-api-dotnet-core-get-started-preview.md)
> * [Java](sql-api-java-get-started.md)
> * [Async Java](sql-api-async-java-get-started.md)
> * [Node.js](sql-api-nodejs-get-started.md)
> 

Questa è un'esercitazione introduttiva per l'API SQL di Azure Cosmos DB. Dopo aver completato questa esercitazione, si otterrà un'app console che consente di creare e ridefinire le query delle risorse Azure Cosmos DB.

Questa esercitazione illustra come:

> [!div class="checklist"]
>
> - Creare un account Azure Cosmos DB e connettersi a esso
> - Configurare una soluzione di Visual Studio
> - Creare un database
> - Creare una raccolta
> - Creare documenti JSON
> - Eseguire query sulla raccolta
> - Aggiornare un documento JSON
> - Eliminare un documento
> - eliminare il database

## <a name="prerequisites"></a>Prerequisiti

Visual Studio 2017 con il flusso di lavoro di sviluppo di Azure installato:
- È possibile scaricare e usare la **versione gratuita** di [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/). Durante l'installazione di Visual Studio abilitare **Sviluppo di Azure**. 

Una sottoscrizione di Azure o un account di prova gratuito di Cosmos DB:
- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
  
- [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]  
  
- Se si usa Azure Cosmos DB Emulator, seguire la procedura illustrata in [Emulatore di Azure Cosmos DB](local-emulator.md) per configurare l'emulatore. Avviare quindi l'esercitazione in [Configurare la soluzione di Visual Studio](#SetupVS).
  
## <a name="get-the-completed-solution"></a>Ottenere la soluzione completa

Se non si ha tempo per completare l'esercitazione o se si vogliono solo gli esempi di codice, è possibile scaricare la soluzione completa da [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

Per eseguire la soluzione completa scaricata: 

1. Assicurarsi di avere i [prerequisiti](#prerequisites) installati. 
1. Aprire il file della soluzione *GetStarted.sln* scaricato in Visual Studio.
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **GetStarted** e scegliere **Gestisci pacchetti NuGet**.
1. Nella scheda **NuGet** selezionare **Ripristina** per ripristinare i riferimenti ad Azure Cosmos DB .NET SDK.
1. Nel file *App.config* aggiornare i valori `EndpointUrl` e `PrimaryKey` come illustrato nella sezione [Connettersi all'account Azure Cosmos DB](#Connect).
1. Selezionare **Debug** > **Avvia senza eseguire debug** o premere **CTRL**+**F5** per compilare ed eseguire l'app.

## <a name="create-an-azure-cosmos-db-account"></a>Creare un account Azure Cosmos DB

Seguire queste istruzioni per creare un account Azure Cosmos DB nel portale di Azure. Se si ha già un account Azure Cosmos DB da usare, ignorare questo passaggio e passare a [Configurare la soluzione di Visual Studio](#SetupVS). 

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Configurare la soluzione in Visual Studio

1. In Visual Studio 2017 selezionare **File** > **Nuovo** > **Progetto**.
   
1. Nella finestra di dialogo **Nuovo progetto** selezionare **Visual C#** > **App console (.NET Framework)**, assegnare al progetto il nome *AzureCosmosDBApp* e quindi fare clic su **OK**.
   
   ![Screenshot della finestra Nuovo progetto](./media/sql-api-get-started/nosql-tutorial-new-project-2.png)
   
1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **AzureCosmosDBApp** e scegliere **Gestisci pacchetti NuGet**.
   
   ![Menu di scelta rapida del progetto](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges.png)
   
1. Nella scheda **NuGet** selezionare **Sfoglia** e immettere *azure documentdb* nella casella di ricerca.
   
1. Trovare e selezionare **Microsoft.Azure.DocumentDB** e selezionare **Installa** se non è già installato.
   
   L'ID pacchetto per la libreria client dell'API SQL di Azure Cosmos DB è [Microsoft Azure Cosmos DB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).

   > [!NOTE]
   > Se si usa .NET Core, vedere [la documentazione di .NET Core](./sql-api-dotnetcore-get-started.md).

   ![Screenshot del menu di NuGet per l'individuazione di Azure Cosmos DB Client SDK](./media/sql-api-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)
   
   Se viene visualizzato un messaggio sull'anteprima delle modifiche alla soluzione, fare clic su **OK**. Se viene visualizzato un messaggio sull'accettazione della licenza, selezionare **Accetto**.

## <a id="Connect"></a>Connettersi all'account Azure Cosmos DB

Si inizierà ora a scrivere il codice. Il file *Project.cs* completo per questa esercitazione è disponibile in [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

1. In **Esplora soluzioni** selezionare *Program.cs* e nell'editor di codice aggiungere i riferimenti seguenti all'inizio del file:
   
   ```csharp
   using System.Net;
   using Microsoft.Azure.Documents;
   using Microsoft.Azure.Documents.Client;
   using Newtonsoft.Json;
   ```
   
1. Aggiungere quindi le due costanti seguenti e la variabile `client` a `public class Program`.
   
   ```csharp
   
   public class Program
   {
      private const string EndpointUrl = "<your endpoint URL>";
      private const string PrimaryKey = "<your primary key>";
      private DocumentClient client;
   ```
   
1. L'URL dell'endpoint e la chiave primaria consentono all'app di connettersi all'account Azure Cosmos DB e consentono all'account Azure Cosmos DB di considerare attendibile la connessione. Copiare le chiavi dal [portale di Azure](https://portal.azure.com) e incollarle nel codice. 

   
   1. Selezionare **Chiavi** nel menu di spostamento a sinistra dell'account Azure Cosmos DB.
      
      ![Visualizzare e copiare le chiavi di accesso nel portale di Azure](./media/sql-api-get-started/nosql-tutorial-keys.png)
      
   1. In **Chiavi di lettura/scrittura** copiare il valore **URI** usando il pulsante di copia a destra e incollarlo in `<your endpoint URL>` in *Program.cs*. Ad esempio:  
      
      `private const string EndpointUrl = "https://mysqlapicosmosdb.documents.azure.com:443/";`
      
   1. Copiare il valore **CHIAVE PRIMARIA** e incollarlo in `<your primary key>` in *Program.cs*. Ad esempio:  
      
      `private const string PrimaryKey = "19ZDNJAiYL26tmnRvoez6hmtIfBGwjun50PWRjNYMC2ig8Ob9hYk7Fq1RYSv8FcIYnh1TdBISvCh7s6yyb0000==";`
   
1. Dopo il metodo `Main` aggiungere una nuova attività asincrona denominata `GetStartedDemo`, che crea un'istanza di un nuovo `DocumentClient` denominato `client`.
   
   ```csharp
      private async Task GetStartedDemo()
      {
        client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
      }
   ```
   
1. Aggiungere il codice seguente al metodo `Main` per eseguire l'attività `GetStartedDemo`. Il metodo `Main` rileva le eccezioni e le scrive nella console.
   
   ```csharp
      static void Main(string[] args)
      {
        try
        {
           Program p = new Program();
           p.GetStartedDemo().Wait();
        }
        catch (DocumentClientException de)
        {
           Exception baseException = de.GetBaseException();
           Console.WriteLine($"{de.StatusCode} error occurred: {de.Message}, Message: {baseException.Message}");
        }
        catch (Exception e)
        {
           Exception baseException = e.GetBaseException();
           Console.WriteLine($"Error: {e.Message}, Message: {baseException.Message}");
        }
        finally
        {
           Console.WriteLine("End of demo, press any key to exit.");
           Console.ReadKey();
        }
      }
   ```
   
1. Premere **F5** per eseguire l'app. 
   
1. Quando viene visualizzato il messaggio **End of demo, press any key to exit** nella finestra della console, significa che la connessione è stata stabilita. Premere un tasto per chiudere la finestra della console. 

La connessione all'account Azure Cosmos DB è stata stabilita correttamente. Ora si useranno alcune risorse di Azure Cosmos DB.  

## <a name="create-a-database"></a>Creare un database

Un [database](databases-containers-items.md#azure-cosmos-databases) Azure Cosmos DB è il contenitore logico per l'archiviazione di documenti JSON partizionato nelle raccolte. Per creare un database, usare il metodo [CreateDatabaseIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync) della classe `DocumentClient`. 

1. Prima di aggiungere il codice per la creazione di un database, aggiungere un metodo helper per la scrittura nella console. Copiare e incollare il metodo `WriteToConsoleAndPromptToContinue` seguente dopo il metodo `GetStartedDemo` nel codice.
   
   ```csharp
   private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
   {
      Console.WriteLine(format, args);
      Console.WriteLine("Press any key to continue...");
      Console.ReadKey();
   }
   ```
   
1. Copiare e incollare la riga seguente nel metodo `GetStartedDemo`, dopo la riga `client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);`. Questo codice crea un database denominato `FamilyDB`.
   
   ```csharp
      await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
   ```
   
1. Premere **F5** per eseguire l'app.

La creazione di un database Azure Cosmos DB è stata completata. Per visualizzare il database nel [portale di Azure](https://portal.azure.com), selezionare **Esplora dati** nel riquadro di spostamento a sinistra dell'account Azure Cosmos DB. 

## <a id="CreateColl"></a>Creare una raccolta

Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata. Per creare una raccolta, usare il metodo [CreateDocumentCollectionIfNotExistsAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync#overloads) della classe `DocumentClient`. 

> [!IMPORTANT]
> **CreateDocumentCollectionIfNotExistsAsync** crea una nuova raccolta con velocità effettiva riservata, che presenta implicazioni in termini di prezzi. Per altre informazioni, visitare la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/cosmos-db/).
> 

1. Copiare e incollare il codice seguente nel metodo `GetStartedDemo`, dopo la riga `await client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });`. Questo codice crea una raccolta di documenti denominata `FamilyCollection`.
   
   ```csharp
      await client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });
   ```
   
1. Premere **F5** per eseguire l'app.

La creazione di una raccolta di documenti di Azure Cosmos DB è stata completata. È possibile visualizzare la raccolta sotto il database **FamilyDB** in **Esplora dati** nel portale di Azure.  

## <a id="CreateDoc"></a>Creare documenti JSON

I documenti sono contenuto JSON arbitrario definito dall'utente. I documenti devono avere una proprietà ID serializzata come `id` in JSON. Per creare i documenti, usare il metodo [CreateDocumentAsync](/dotnet/api/microsoft.azure.documents.client.documentclient.createdocumentasync#overloads) della classe `DocumentClient`. 

> [!TIP]
> Se sono già disponibili dati da archiviare nel database, è possibile usare lo [strumento di migrazione dei dati](import-data.md) di Azure Cosmos DB per importarli.
>

Il codice seguente crea e inserisce i due documenti nella raccolta di database. Prima si creano una classe `Family` e le sottoclassi `Parent`, `Child`, `Pet` e `Address` da usare in `Family`. Si crea quindi un metodo `CreateFamilyDocumentIfNotExists` e infine si creano e si inseriscono due documenti. 

1. Copiare e incollare le classi `Family`, `Parent`, `Child`, `Pet` e `Address` seguenti dopo il metodo `WriteToConsoleAndPromptToContinue` nel codice.
   
   ```csharp
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
   ```
   
1. Copiare e incollare il metodo `CreateFamilyDocumentIfNotExists` seguente dopo la classe `Address` appena aggiunta.
   
   ```csharp
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            WriteToConsoleAndPromptToContinue($"Found {family.Id}");
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                WriteToConsoleAndPromptToContinue($"Created Family {family.Id}");
            }
            else
            {
                throw;
            }
        }
    }
   ```
   
1. Copiare e incollare il codice seguente alla fine del metodo `GetStartedDemo`, dopo la riga `await client.CreateDocumentCollectionIfNotExistsAsync`. Questo codice crea e inserisce due documenti, rispettivamente per le famiglie Andersen e Wakefield.
   
   ```csharp
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
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
        IsRegistered = true
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        LastName = "Wakefield",
        Parents = new Parent[]
        {
            new Parent { FamilyName = "Wakefield", FirstName = "Robin" },
            new Parent { FamilyName = "Miller", FirstName = "Ben" }
        },
        Children = new Child[]
        {
            new Child
            {
                FamilyName = "Merriam",
                FirstName = "Jesse",
                Gender = "female",
                Grade = 8,
                Pets = new Pet[]
                {
                    new Pet { GivenName = "Goofy" },
                    new Pet { GivenName = "Shadow" }
                }
            },
            new Child
            {
                FamilyName = "Miller",
                FirstName = "Lisa",
                Gender = "female",
                Grade = 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);
   ```
   
1. Premere **F5** per eseguire l'app.

La creazione di due documenti di Azure Cosmos DB è stata completata. È possibile visualizzare i documenti sotto il database **FamilyDB** e la raccolta **FamilyCollection** in **Esplora dati** nel portale di Azure.   

![Diagramma che illustra la relazione gerarchica tra l'account, il database online, la raccolta e i documenti](./media/sql-api-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Eseguire query sulle risorse di Azure Cosmos DB

Azure Cosmos DB supporta [query complesse](how-to-sql-query.md) sui documenti JSON archiviati nelle raccolte. L'esempio di codice seguente usa la sintassi SQL di Azure Cosmos DB e LINQ per eseguire una query sui documenti di esempio.

1. Copiare e incollare il metodo `ExecuteSimpleQuery` seguente dopo il metodo `CreateFamilyDocumentIfNotExists` nel codice.
   
   ```csharp
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options.
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

        // Find the Andersen family by its LastName.
        IQueryable<Family> familyQuery = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
            .Where(f => f.LastName == "Andersen");

        // Execute the query synchronously. 
        // You could also execute it asynchronously using the IDocumentQuery<T> interface.
        Console.WriteLine("Running LINQ query...");
        foreach (Family family in familyQuery)
        {
            Console.WriteLine($"\tRead {family}");
        }

        // Now execute the same query using direct SQL.
        IQueryable<Family> familyQueryInSql = client.CreateDocumentQuery<Family>(
            UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
            "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
            queryOptions);

        Console.WriteLine("Running direct SQL query...");
        foreach (Family family in familyQueryInSql)
        {
            Console.WriteLine($"\tRead {family}");
        }

        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }
   ```
   
1. Copiare e incollare il codice seguente alla fine del metodo `GetStartedDemo`, dopo la riga `await CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);`.
   
   ```csharp
      ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Premere **F5** per eseguire l'app.

La query precedente restituisce l'elemento completo per la famiglia Andersen. L'esecuzione di una query in una raccolta di Azure Cosmos DB è stata completata.

Il diagramma seguente illustra il modo in cui la sintassi di query SQL di Azure Cosmos DB viene chiamata nella raccolta. La stessa logica si applica alla query LINQ.

![Diagramma che illustra l'ambito e il significato della query usata nell'esercitazione su NoSQL per creare un'applicazione console C#](./media/sql-api-get-started/nosql-tutorial-collection-documents.png)

La parola chiave [FROM](how-to-sql-query.md#FromClause) nella query SQL è facoltativa perché le query di Azure Cosmos DB sono già limitate a una singola raccolta. È possibile effettuare lo swapping di `FROM Families f` con `FROM root r` o qualsiasi altro nome di variabile scelto. Azure Cosmos DB dedurrà che `Families`, `root` o il nome della variabile scelto, si riferisce alla raccolta attuale.

## <a id="ReplaceDocument"></a>Aggiornare un documento JSON

L'API SQL di Azure Cosmos DB supporta l'aggiornamento e la sostituzione di documenti JSON.  

1. Copiare e incollare il metodo `ReplaceFamilyDocument` seguente dopo il metodo `ExecuteSimpleQuery` nel codice.
   
   ```csharp
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
       await client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
       WriteToConsoleAndPromptToContinue($"Replaced Family {familyName}");
    }
   ```
   
1. Copiare e incollare il codice seguente alla fine del metodo `GetStartedDemo`, dopo la riga `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");`. Il codice aggiorna i dati in uno dei documenti e quindi esegue di nuovo la query per visualizzare il documento modificato.
   
   ```csharp
   // Update the Grade of the Andersen Family child
   andersenFamily.Children[0].Grade = 6;
   await ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily", andersenFamily);
   ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
   ```
   
1. Premere **F5** per eseguire l'app.

L'output della query indica che l'elemento `Grade` per il figlio della famiglia Andersen è stato aggiornato da `5` a `6`. L'aggiornamento e la sostituzione di un documento di Azure Cosmos DB sono stati completati. 

## <a id="DeleteDocument"></a>Eliminare un documento JSON

L'API SQL di Azure Cosmos DB supporta l'eliminazione di documenti JSON.  

1. Copiare e incollare il metodo `DeleteFamilyDocument` seguente dopo il metodo `ReplaceFamilyDocument`.
   
   ```csharp
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
        await client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
        Console.WriteLine($"Deleted Family {documentName}");
    }
   ```
   
1. Copiare e incollare il codice seguente alla fine del metodo `GetStartedDemo`, dopo la seconda riga `ExecuteSimpleQuery("FamilyDB", "FamilyCollection");`.
   
   ```csharp
   await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");
   ```
   
1. Premere **F5** per eseguire l'app.

L'eliminazione di un documento di Azure Cosmos DB è stata completata. 

## <a id="DeleteDatabase"></a>Eliminare il database

Eliminare il database creato per rimuoverlo con tutte le risorse figlio, inclusi la raccolta e i documenti. 

1. Copiare e incollare il codice seguente alla fine del metodo `GetStartedDemo`, dopo la riga `await DeleteFamilyDocument("FamilyDB", "FamilyCollection", "AndersenFamily");`. 
   
   ```csharp
   // Clean up - delete the database
   await client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));
   ```
   
1. Premere **F5** per eseguire l'app.

L'eliminazione del database Azure Cosmos DB è stata completata. In **Esplora dati** per l'account Azure Cosmos DB è possibile verificare che il database FamilyDB è stato eliminato. 

## <a id="Run"></a>Eseguire l'intera app console C#

Premere **F5** in Visual Studio per compilare ed eseguire l'app console C# completa in modalità di debug. Nella finestra della console verrà visualizzato l'output seguente:

```bash
Created Family AndersenFamily
Press any key to continue ...
 Created Family WakefieldFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Replaced Family AndersenFamily
Press any key to continue ...
 Running LINQ query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Running direct SQL query...
        Read {"id":"AndersenFamily","LastName":"Andersen","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
Press any key to continue ...
 Deleted Family AndersenFamily
End of demo, press any key to exit.
```

Congratulazioni! Ora che l'esercitazione è stata completata, si dispone di un'app console C# funzionante che crea, esegue query, aggiorna ed elimina le risorse di Azure Cosmos DB.  

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su Azure Cosmos DB, vedere [Introduzione ad Azure Cosmos DB](introduction.md).
* Per un'esercitazione su MVC ASP.NET più complessa, vedere [Esercitazione su MVC ASP.NET: sviluppo di applicazioni Web con Azure Cosmos DB](sql-api-dotnet-application.md).
* Per eseguire i test della scalabilità e delle prestazioni con Azure Cosmos DB, vedere [Test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md).
* Per informazioni su come monitorare le richieste, l'utilizzo e le risorse di archiviazione di Azure Cosmos DB, vedere [Monitorare gli account](monitor-accounts.md).
* Eseguire query su un set di dati di esempio nella pagina [Query Playground](https://www.documentdb.com/sql/demo).

