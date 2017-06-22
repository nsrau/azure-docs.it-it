---
title: 'Azure Cosmos DB: Esercitazione introduttiva per l&quot;API DocumentDB | Microsoft Docs'
description: Esercitazione che crea un database online e un&quot;applicazione console in C# con l&quot;API DocumentDB.
keywords: esercitazione su nosql, database online, applicazione console C#
services: cosmos-db
documentationcenter: .net
author: AndrewHoh
manager: jhubbard
editor: monicar
ms.assetid: bf08e031-718a-4a2a-89d6-91e12ff8797d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 05/22/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 5855b0542ca2ec58f3a24efcc2cf7a96fb92da0f
ms.contentlocale: it-it
ms.lasthandoff: 05/31/2017


---
# <a name="azure-cosmos-db-documentdb-api-getting-started-tutorial"></a>Azure Cosmos DB: Esercitazione introduttiva per l'API DocumentDB
> [!div class="op_single_selector"]
> * [.NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Node.js per MongoDB](mongodb-samples.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 

Esercitazione introduttiva per l'API DocumentDB di Azure Cosmos DB Dopo aver seguito questa esercitazione, si otterrà un'applicazione console che consente di creare e ridefinire le query delle risorse DocumentDB.

Tratteremo questo argomento:

* Creazione e connessione a un account Azure Cosmos DB
* Configurare una soluzione Visual Studio
* Creazione di un database online
* Creare una raccolta
* Creazione di documenti JSON
* Esecuzione di query sulla raccolta
* Sostituzione di un documento
* Eliminazione di un documento
* Eliminazione del database

Non si ha tempo? Nessun problema. La soluzione completa è disponibile in [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). Per istruzioni rapide, vedere la sezione [Ottenere la soluzione completa per l'esercitazione su NoSQL](#GetSolution).

Successivamente, utilizzare i pulsanti di voti all'inizio o alla fine di questa pagina per fornire feedback. Se si desidera contattarci, è possibile includere l'indirizzo di posta elettronica nel commento per il follow-up.

Ecco come procedere.

## <a name="prerequisites"></a>Prerequisiti
Assicurarsi di disporre di quanto segue:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/). 
    * In alternativa, per questa esercitazione è possibile usare l'[emulatore Azure Cosmos DB](local-emulator.md).
* [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com/).

## <a name="step-1-create-an-azure-cosmos-db-account"></a>Passaggio 1: Creare un account di Azure Cosmos DB
Creare prima di tutto un account Azure Cosmos DB. Se si ha già un account, è possibile ignorare questo passaggio e passare a [Configurare la soluzione Visual Studio](#SetupVS). Se si usa l'emulatore Azure Cosmos DB, seguire i passaggi descritti nell'articolo [Azure Cosmos DB Emulator](local-emulator.md) (Emulatore Azure Cosmos DB) per configurare l'emulatore e proseguire con il passaggio [Configurare la soluzione di Visual Studio](#SetupVS).

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a id="SetupVS"></a>Passaggio 2: Configurare la soluzione di Visual Studio
1. Aprire **Visual Studio 2015** nel computer.
2. Scegliere **Nuovo** dal menu **File** e quindi selezionare **Progetto**.
3. Nella finestra di dialogo **Nuovo progetto** selezionare **Modelli** / **Visual C#** / **Applicazione console**, assegnare un nome al progetto e quindi fare clic su **OK**.
   ![Screenshot della finestra Nuovo progetto](./media/documentdb-get-started/nosql-tutorial-new-project-2.png)
4. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla nuova applicazione console, disponibile nella soluzione di Visual Studio, quindi scegliere **Gestisci pacchetti NuGet**.
    
    ![Screenshot del menu selezionato con il pulsante destro del mouse per il progetto](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges.png)
5. Nella scheda **NuGet** fare clic su **Sfoglia** e digitare **azure documentdb** nella casella di ricerca.
6. Nei risultati trovare **Microsoft.Azure.DocumentDB** e fare clic su **Installa**.
   L'ID pacchetto per la libreria client dell'API DocumentDB di Azure Cosmos DB è [Microsoft Azure DocumentDB Client Library](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/).
   ![Screenshot del menu di NuGet per l'individuazione dell'SDK client di Azure Cosmos DB](./media/documentdb-get-started/nosql-tutorial-manage-nuget-pacakges-2.png)

    Se viene visualizzato un messaggio sulla verifica delle modifiche alla soluzione, fare clic su **OK**. Se viene visualizzato un messaggio sull'accettazione della licenza, fare clic su **Accetto**.

L'installazione è riuscita. Ora che abbiamo completato l'installazione, iniziamo a scrivere il codice. Un progetto di codice completo di questa esercitazione è disponibile in [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs).

## <a id="Connect"></a>Passaggio 3: Connettersi a un account Azure Cosmos DB
In primo luogo, aggiungere questi riferimenti all'inizio dell'applicazione c#, nel file Program.cs:

    using System;
    using System.Linq;
    using System.Threading.Tasks;

    // ADD THIS PART TO YOUR CODE
    using System.Net;
    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Newtonsoft.Json;

> [!IMPORTANT]
> Per completare l'esercitazione, assicurarsi di aggiungere le dipendenze illustrate sopra.
> 
> 

Aggiungere ora queste due costanti e la variabile *client* sotto la classe pubblica *Program*.

    public class Program
    {
        // ADD THIS PART TO YOUR CODE
        private const string EndpointUrl = "<your endpoint URL>";
        private const string PrimaryKey = "<your primary key>";
        private DocumentClient client;

Tornare al [portale di Azure](https://portal.azure.com) per recuperare l'URL e la chiave primaria dell'endpoint. L'URL e la chiave primaria dell'endpoint sono necessari all'applicazione per conoscere la destinazione della connessione e ad Azure Cosmos DB per considerare attendibile la connessione dell'applicazione.

Nel portale di Azure passare all'account Azure Cosmos DB e quindi fare clic su **Chiavi**.

Copiare l'URI dal portale e incollarlo in `<your endpoint URL>` nel file program.cs. Copiare quindi la CHIAVE PRIMARIA dal portale e incollarla in `<your primary key>`.

![Screenshot del portale di Azure usato nell'esercitazione su NoSQL per creare un'applicazione console C#. Mostra un account Azure Cosmos DB, con l'hub ACTIVE evidenziato, il pulsante CHIAVI evidenziato nel pannello dell'account Azure Cosmos DB e i valori di URI, CHIAVE PRIMARIA e CHIAVE SECONDARIA evidenziati nel pannello Chiavi][keys]

Verrà quindi avviata l'applicazione tramite la creazione di una nuova istanza di **DocumentClient**.

Sotto il metodo **Main** aggiungere la nuova attività asincrona denominata **GetStartedDemo** che creerà la nuova istanza di **DocumentClient**.

    static void Main(string[] args)
    {
    }

    // ADD THIS PART TO YOUR CODE
    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);
    }

Aggiungere questo codice per eseguire l'attività asincrona dal metodo **Main** . Il metodo **Main** rileva le eccezioni e le scrive nella console.

    static void Main(string[] args)
    {
            // ADD THIS PART TO YOUR CODE
            try
            {
                    Program p = new Program();
                    p.GetStartedDemo().Wait();
            }
            catch (DocumentClientException de)
            {
                    Exception baseException = de.GetBaseException();
                    Console.WriteLine("{0} error occurred: {1}, Message: {2}", de.StatusCode, de.Message, baseException.Message);
            }
            catch (Exception e)
            {
                    Exception baseException = e.GetBaseException();
                    Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
            }
            finally
            {
                    Console.WriteLine("End of demo, press any key to exit.");
                    Console.ReadKey();
            }

Premere **F5** per eseguire l'applicazione. L'output della finestra della console mostra il messaggio `End of demo, press any key to exit.`, che conferma che è stata stabilita la connessione.  È quindi possibile chiudere la finestra della console. 

Congratulazioni. La connessione a un account Azure Cosmos DB è stata stabilita. Ora è possibile esaminare l'uso delle risorse di Azure Cosmos DB.  

## <a name="step-4-create-a-database"></a>Passaggio 4: Creare un database
Prima di aggiungere il codice per la creazione di un database, aggiungere un metodo helper per la scrittura nella console.

Copiare e incollare il metodo **WriteToConsoleAndPromptToContinue** dopo il metodo **GetStartedDemo**.

    // ADD THIS PART TO YOUR CODE
    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
            Console.WriteLine(format, args);
            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

È possibile creare un [database](documentdb-resources.md#databases) di Azure Cosmos DB usando il metodo [CreateDatabaseIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseifnotexistsasync.aspx) della classe **DocumentClient**. Un database è il contenitore logico per l'archiviazione di documenti JSON partizionato nelle raccolte.

Copiare e incollare il codice seguente nel metodo **GetStartedDemo** dopo il codice di creazione del client. Verrà creato un database denominato *FamilyDB*.

    private async Task GetStartedDemo()
    {
        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        // ADD THIS PART TO YOUR CODE
        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

Premere **F5** per eseguire l'applicazione.

Congratulazioni. La creazione di un database di Azure Cosmos DB è stata completata.  

## <a id="CreateColl"></a>Passaggio 5: Creare una raccolta
> [!WARNING]
> **CreateDocumentCollectionIfNotExistsAsync** crea una nuova raccolta con velocità effettiva riservata, che presenta implicazioni in termini di prezzi. Per altre informazioni, visitare la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/documentdb/).
> 
> 

È possibile creare una [raccolta](documentdb-resources.md#collections) usando il metodo [CreateDocumentCollectionIfNotExistsAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionifnotexistsasync.aspx) della classe **DocumentClient**. Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata.

Copiare e incollare il codice seguente nel metodo **GetStartedDemo** dopo il codice di creazione del database. Verrà creata una raccolta di documenti denominata *FamilyCollection*.

        this.client = new DocumentClient(new Uri(EndpointUrl), PrimaryKey);

        await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });

        // ADD THIS PART TO YOUR CODE
         await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });

Premere **F5** per eseguire l'applicazione.

Congratulazioni. La creazione di una raccolta di documenti di Azure Cosmos DB è stata completata.  

## <a id="CreateDoc"></a>Passaggio 6: Creare documenti JSON
È possibile creare un [documento](documentdb-resources.md#documents) usando il metodo [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) della classe **DocumentClient**. I documenti sono contenuto JSON definito dall'utente (arbitrario). Ora è possibile inserire uno o più documenti. Se sono già disponibili dati da archiviare nel database, è possibile usare lo [strumento di migrazione dei dati](import-data.md) di DocumentDB per importare i dati in un database.

È necessario creare prima di tutto una classe **Family** che rappresenterà gli oggetti archiviati in Azure Cosmos DB in questo esempio. Verranno create anche le sottoclassi **Parent**, **Child**, **Pet** e **Address** da usare in **Family**. Si noti che i documenti devono avere una proprietà **Id** serializzata come **id** in JSON. Creare queste classi aggiungendo le classi secondarie interne seguenti dopo il metodo **GetStartedDemo** .

Copiare e incollare le classi **Family**, **Parent**, **Child**, **Pet** e **Address** dopo il metodo **WriteToConsoleAndPromptToContinue**.

    private void WriteToConsoleAndPromptToContinue(string format, params object[] args)
    {
        Console.WriteLine(format, args);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
    }

    // ADD THIS PART TO YOUR CODE
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

Copiare e incollare il metodo **CreateFamilyDocumentIfNotExists** sotto il metodo **Address**.

    // ADD THIS PART TO YOUR CODE
    private async Task CreateFamilyDocumentIfNotExists(string databaseName, string collectionName, Family family)
    {
        try
        {
            await this.client.ReadDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, family.Id));
            this.WriteToConsoleAndPromptToContinue("Found {0}", family.Id);
        }
        catch (DocumentClientException de)
        {
            if (de.StatusCode == HttpStatusCode.NotFound)
            {
                await this.client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), family);
                this.WriteToConsoleAndPromptToContinue("Created Family {0}", family.Id);
            }
            else
            {
                throw;
            }
        }
    }

Inserire anche due documenti, relativi rispettivamente alla famiglia Andersen e alla famiglia Wakefield.

Copiare e incollare il codice seguente nel metodo **GetStartedDemo** dopo il codice di creazione della raccolta di documenti.

    await this.client.CreateDatabaseIfNotExistsAsync(new Database { Id = "FamilyDB" });
    
    await this.client.CreateDocumentCollectionIfNotExistsAsync(UriFactory.CreateDatabaseUri("FamilyDB"), new DocumentCollection { Id = "FamilyCollection" });


    // ADD THIS PART TO YOUR CODE
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
            IsRegistered = true
    };

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", andersenFamily);

    Family wakefieldFamily = new Family
    {
            Id = "Wakefield.7",
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

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

Premere **F5** per eseguire l'applicazione.

Congratulazioni. La creazione di due documenti di Azure Cosmos DB è stata completata.  

![Diagramma che illustra la relazione gerarchica tra l'account, il database online, la raccolta e i documenti usati nell'esercitazione su NoSQL per creare un'applicazione console C#](./media/documentdb-get-started/nosql-tutorial-account-database.png)

## <a id="Query"></a>Passaggio 7: Eseguire query sulle risorse di Azure Cosmos DB
Azure Cosmos DB supporta [query complesse](documentdb-sql-query.md) sui documenti JSON archiviati in ogni raccolta.  Il codice di esempio seguente mostra diverse query che usano la sintassi SQL di Azure Cosmos DB e LINQ e che possono essere eseguite sui documenti inseriti nel passaggio precedente.

Copiare e incollare il metodo **ExecuteSimpleQuery** dopo il metodo **CreateFamilyDocumentIfNotExists**.

    // ADD THIS PART TO YOUR CODE
    private void ExecuteSimpleQuery(string databaseName, string collectionName)
    {
        // Set some common query options
        FeedOptions queryOptions = new FeedOptions { MaxItemCount = -1 };

            // Here we find the Andersen family via its LastName
            IQueryable<Family> familyQuery = this.client.CreateDocumentQuery<Family>(
                    UriFactory.CreateDocumentCollectionUri(databaseName, collectionName), queryOptions)
                    .Where(f => f.LastName == "Andersen");

            // The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
            Console.WriteLine("Running LINQ query...");
            foreach (Family family in familyQuery)
            {
                    Console.WriteLine("\tRead {0}", family);
            }

            // Now execute the same query via direct SQL
            IQueryable<Family> familyQueryInSql = this.client.CreateDocumentQuery<Family>(
                    UriFactory.CreateDocumentCollectionUri(databaseName, collectionName),
                    "SELECT * FROM Family WHERE Family.LastName = 'Andersen'",
                    queryOptions);

            Console.WriteLine("Running direct SQL query...");
            foreach (Family family in familyQueryInSql)
            {
                    Console.WriteLine("\tRead {0}", family);
            }

            Console.WriteLine("Press any key to continue ...");
            Console.ReadKey();
    }

Copiare e incollare il codice seguente nel metodo **GetStartedDemo** dopo il codice di creazione del secondo documento.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    // ADD THIS PART TO YOUR CODE
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Premere **F5** per eseguire l'applicazione.

Congratulazioni. L'esecuzione di una query in una raccolta di Azure Cosmos DB è stata completata.

Il diagramma seguente illustra il modo in cui la sintassi di query di SQL di Azure Cosmos DB viene chiamata sulla raccolta creata e la stessa logica viene applicata alla query di LINQ.

![Diagramma che illustra l'ambito e il significato della query usata nell'esercitazione su NoSQL per creare un'applicazione console C#](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

La parola chiave [FROM](documentdb-sql-query.md#FromClause) è facoltativa nella query perché le query di DocumentDB sono già limitate a una singola raccolta. Di conseguenza, "FROM Families f" può essere scambiata con "FROM root r" o con il nome di qualsiasi altra variabile scelta. DocumentDB dedurrà che Families, root o il nome della variabile scelta, si riferisce per impostazione predefinita alla raccolta attuale.

## <a id="ReplaceDocument"></a>Passaggio 8: Sostituire un documento JSON
Azure Cosmos DB supporta la sostituzione di documenti JSON.  

Copiare e incollare il metodo **ReplaceFamilyDocument** dopo il metodo **ExecuteSimpleQuery**.

    // ADD THIS PART TO YOUR CODE
    private async Task ReplaceFamilyDocument(string databaseName, string collectionName, string familyName, Family updatedFamily)
    {
         await this.client.ReplaceDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, familyName), updatedFamily);
         this.WriteToConsoleAndPromptToContinue("Replaced Family {0}", familyName);
    }

Copiare e incollare il codice seguente nel metodo **GetStartedDemo** dopo il codice di esecuzione della query, alla fine del metodo. Dopo aver sostituito il documento, verrà eseguita di nuovo la stessa query per visualizzare il documento modificato.

    await this.CreateFamilyDocumentIfNotExists("FamilyDB", "FamilyCollection", wakefieldFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    // ADD THIS PART TO YOUR CODE
    // Update the Grade of the Andersen Family child
    andersenFamily.Children[0].Grade = 6;

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

Premere **F5** per eseguire l'applicazione.

Congratulazioni. La sostituzione di un documento di Azure Cosmos DB è stata completata.

## <a id="DeleteDocument"></a>Passaggio 9: Eliminare un documento JSON
Azure Cosmos DB supporta l'eliminazione di documenti JSON.  

Copiare e incollare il metodo **DeleteFamilyDocument** dopo il metodo **ReplaceFamilyDocument**.

    // ADD THIS PART TO YOUR CODE
    private async Task DeleteFamilyDocument(string databaseName, string collectionName, string documentName)
    {
         await this.client.DeleteDocumentAsync(UriFactory.CreateDocumentUri(databaseName, collectionName, documentName));
         Console.WriteLine("Deleted Family {0}", documentName);
    }

Copiare e incollare il codice seguente nel metodo **GetStartedDemo** dopo il codice di esecuzione della seconda query, alla fine del metodo.

    await this.ReplaceFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1", andersenFamily);
    
    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");
    
    // ADD THIS PART TO CODE
    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

Premere **F5** per eseguire l'applicazione.

Congratulazioni. L'eliminazione di un documento di Azure Cosmos DB è stata completata.

## <a id="DeleteDatabase"></a>Passaggio 10: Eliminare il database
Se si elimina il database creato, verrà rimosso il database e tutte le risorse figlio (raccolte, documenti e così via).

Copiare e incollare il codice seguente nel metodo **GetStartedDemo** dopo il codice di eliminazione del documento per eliminare l'intero database e tutte le risorse figlio.

    this.ExecuteSimpleQuery("FamilyDB", "FamilyCollection");

    await this.DeleteFamilyDocument("FamilyDB", "FamilyCollection", "Andersen.1");

    // ADD THIS PART TO CODE
    // Clean up/delete the database
    await this.client.DeleteDatabaseAsync(UriFactory.CreateDatabaseUri("FamilyDB"));

Premere **F5** per eseguire l'applicazione.

Congratulazioni. L'eliminazione di un database di Azure Cosmos DB è stata completata.

## <a id="Run"></a>Passaggio 11: Eseguire l'intera applicazione console C#
Premere F5 in Visual Studio per compilare l'applicazione in modalità di debug.

Verrà visualizzato l'output dell'app introduttiva. L'output visualizzerà i risultati delle query aggiunte e dovrà corrispondere al testo di esempio riportato di seguito.

    Created FamilyDB
    Press any key to continue ...
    Created FamilyCollection
    Press any key to continue ...
    Created Family Andersen.1
    Press any key to continue ...
    Created Family Wakefield.7
    Press any key to continue ...
    Running LINQ query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Running direct SQL query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":5,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Replaced Family Andersen.1
    Press any key to continue ...
    Running LINQ query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Running direct SQL query...
        Read {"id":"Andersen.1","LastName":"Andersen","District":"WA5","Parents":[{"FamilyName":null,"FirstName":"Thomas"},{"FamilyName":null,"FirstName":"Mary Kay"}],"Children":[{"FamilyName":null,"FirstName":"Henriette Thaulow","Gender":"female","Grade":6,"Pets":[{"GivenName":"Fluffy"}]}],"Address":{"State":"WA","County":"King","City":"Seattle"},"IsRegistered":true}
    Deleted Family Andersen.1
    End of demo, press any key to exit.

Congratulazioni. L'esercitazione è stata completata ed è stata creata un'applicazione console C# funzionante.

## <a id="GetSolution"></a> Ottenere la soluzione completa per l'esercitazione
Se non si ha tempo per completare le procedure dell'esercitazione o se si vogliono solo scaricare gli esempi di codice, è possibile ottenerli da [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). 

Per compilare la soluzione GetStarted, sono necessari gli elementi seguenti:

* Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere un [account gratuito](https://azure.microsoft.com/free/).
* Un [account Azure Cosmos DB][cosmos-db-create-account].
* La soluzione [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) disponibile su GitHub.

Per ripristinare i riferimenti a DocumentDB .NET SDK in Visual Studio, fare clic con il pulsante destro del mouse sulla soluzione **GetStarted** in Esplora soluzioni e quindi scegliere **Abilita ripristino dei pacchetti NuGet**. Nel file App.config aggiornare quindi i valori EndpointUrl e AuthorizationKey come illustrato nell'articolo [Connettersi a un account Azure Cosmos DB](#Connect).

Per completare la procedura, è sufficiente procedere alla compilazione.


## <a name="next-steps"></a>Passaggi successivi
* Per un'esercitazione su MVC ASP.NET più complessa, vedere [Creare un'applicazione Web con MVC ASP.NET usando Azure Cosmos DB](documentdb-dotnet-application.md).
* Per la scalabilità e i test delle prestazioni con Azure Cosmos DB, vedere [Test delle prestazioni e della scalabilità con Azure Cosmos DB](performance-testing.md).
* Informazioni su come [monitorare un account Azure Cosmos DB](monitor-accounts.md).
* Eseguire query sul set di dati di esempio illustrato nella pagina [Query Playground](https://www.documentdb.com/sql/demo).
* Per altre informazioni sul modello di programmazione, vedere la sezione relativa allo sviluppo nella pagina [Documentazione di Azure Cosmos DB](https://azure.microsoft.com/documentation/services/documentdb/).

[keys]: media/documentdb-get-started/nosql-tutorial-keys.png
[cosmos-db-create-account]: create-documentdb-dotnet.md#create-account

