<properties title="Introduzione a un account DocumentDB" pageTitle="Introduzione a un account DocumentDB | Azure" description="Informazioni su come creare e configurare un account DocumentDB, creare database, creare raccolte e archiviare documenti JSON nell'account." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, getting started"   services="documentdb" solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

# Introduzione a un account DocumentDB

In questa guida viene illustrato come usare **Azure DocumentDB (Anteprima)**. Negli esempi, scritti in codice C#, viene usato DocumentDB .NET SDK. Gli scenari presentati includono la creazione e la configurazione di un account DocumentDB, la creazione di database, la creazione di raccolte e l'archiviazione di documenti JSON nell'account. Per altre informazioni sull'uso di Azure DocumentDB, fare riferimento alla sezione Passaggi successivi.

Per usare questa guida introduttiva, è necessario un account DocumentDB e la chiave d'accesso (primaria o secondaria) dell'account. Per altre informazioni, vedere:

-   [Creare un account DocumentDB][Creare un account DocumentDB]

## Sommario

-   [Connettersi a un account DocumentDB][Connettersi a un account DocumentDB]
-   [Creare un database][Creare un database]
-   [Creare una raccolta][Creare una raccolta]
-   [Creare i documenti][Creare i documenti]
-   [Eseguire query sulle risorse di DocumentDB][Eseguire query sulle risorse di DocumentDB]
-   [Passaggi successivi][Passaggi successivi]

## <span id="Connect"></span></a>Connettersi a un account DocumentDB

Sono disponibili diversi SDK e API per usare DocumentDB a livello di codice. Gli esempi seguenti vengono visualizzati in codice C# e usano DocumentDB .NET SDK.

Si inizia creando DocumentClient per stabilire una connessione all'account DocumentDB. Nell'applicazione C# sono necessari i seguenti riferimenti:

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;

L'istanza di DocumentClient può essere creata usando l'endpoint dell'account DocumentDB e la chiave di accesso primaria o secondaria associata all'account.

L'endpoint e le chiavi dell'account DocumentDB sono disponibili nel pannello del portale di anteprima di gestione Azure per l'account DocumentDB.

![][0]

> Le chiavi di accesso DocumentDB disponibili mediante il pannello delle chiavi consentono l'accesso amministrativo all'account DocumentDB e alle risorse contenute. DocumentDB supporta anche l'uso delle chiavi di risorsa che consentono ai client di leggere, scrivere ed eliminare le risorse nell'account DocumentDB in base alle autorizzazioni concesse, senza bisogno di una chiave account.

Creare un client usando un codice come quello contenuto nel seguente esempio.

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";

    // Create a new instance of the DocumentClient
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

**Avviso:** Non archiviare mai le credenziali nel codice sorgente. Per semplicità, in questo esempio verranno visualizzate nel codice sorgente. Vedere la sezione che descrive [come funzionano le stringhe di applicazione e di connessione in Siti Web di Microsoft Azure][come funzionano le stringhe di applicazione e di connessione in Siti Web di Microsoft Azure] per informazioni su come archiviare le credenziali.

Dopo aver appreso come connettersi a un account DocumentDB e creare un'istanza di DocumentClient, è possibile passare alle modalità di utilizzo delle risorse di DocumentDB.

## <span id="CreateDB"></span></a>Creare un database

Con .NET SDK è possibile creare un database DocumentDB con il metodo CreateDatabaseAsync di DocumentClient.

    // Create a Database
    Database database = await client.CreateDatabaseAsync(
        new Database
            {
                Id = "FamilyRegistry"
            });

## <span id="CreateColl"></span></a>Creare una raccolta

Con .NET SDK è possibile creare una raccolta DocumentDB con il metodo CreateDocumentCollectionAsync di DocumentClient. Il database creato nel passaggio precedente ha diverse proprietà, una delle quali è CollectionsLink. Con queste informazioni ora è possibile creare una raccolta.

    // Create a document collection
    DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
        new DocumentCollection
            {
                Id = "FamilyCollection"
            });

## <span id="CreateDoc"></span></a>Creare i documenti

Con .NET SDK è possibile creare un documento DocumentDB con il metodo CreateDocumentAsync di DocumentClient. La raccolta creata nel passaggio precedente ha diverse proprietà, una delle quali è DocumentsLink. Con queste informazioni ora è possibile inserire uno o più documenti. Ai fini di questo esempio, si presuppone l'esistenza di una classe Family che descrive gli attributi di una famiglia, ad esempio nome, genere ed età.

    // Create the Andersen Family document
    Family andersenFamily = new Family
    {
        Id = "AndersenFamily",
        LastName = "Andersen",
        Parents =  new Parent[] {
            new Parent { FirstName = "Thomas" },
            new Parent { FirstName = "Mary Kay"}
        },
        Children = new Child[] {
            new Child { 
                FirstName = "Henriette Thaulow", 
                Gender = "female", 
                Grade = 5, 
                Pets = new Pet[] {
                    new Pet { GivenName = "Fluffy" } 
                }
            } 
        },
        Address = new Address { State = "WA", County = "King", City = "Seattle" },
        IsRegistered = true
    };

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, andersenFamily);

    // Create the WakeField Family document
    Family wakefieldFamily = new Family
    {
        Id = "WakefieldFamily",
        Parents = new Parent[] {
            new Parent { FamilyName= "Wakefield", FirstName= "Robin" },
            new Parent { FamilyName= "Miller", FirstName= "Ben" }
        },
        Children = new Child[] {
            new Child {
                FamilyName= "Merriam", 
                FirstName= "Jesse", 
                Gender= "female", 
                Grade= 8,
                Pets= new Pet[] {
                    new Pet { GivenName= "Goofy" },
                    new Pet { GivenName= "Shadow" }
                }
            },
            new Child {
                FamilyName= "Miller", 
                FirstName= "Lisa", 
                Gender= "female", 
                Grade= 1
            }
        },
        Address = new Address { State = "NY", County = "Manhattan", City = "NY" },
        IsRegistered = false
    };

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, wakefieldFamily);

## <span id="Query"></span></a>Eseguire query sulle risorse di DocumentDB

DocumentDB supporta le query complesse sui documenti JSON archiviati in ciascuna raccolta. Il codice di esempio seguente mostra diverse query che usano la sintassi SQL di DocumentDB e LINQ e che è possibile eseguire sui documenti inseriti nel passaggio precedente.

    // Query the documents using DocumentDB SQL for the Andersen family
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family
    families =
        from f in client.CreateDocumentQuery(documentCollection.DocumentsLink)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family
    families = client.CreateDocumentQuery(documentCollection.DocumentsLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    // Query the documents using DocumentSQl with one join
    var items = client.CreateDocumentQuery<dynamic>(documentCollection.DocumentsLink,
        "SELECT f.id, c.FirstName AS child " +
        "FROM Families f " +
        "JOIN c IN f.Children");

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

    // Query the documents using LINQ with one join
    items = client.CreateDocumentQuery<Family>(documentCollection.DocumentsLink)
        .SelectMany(family => family.Children
            .Select(children => new
            {
                family = family.Id,
                child = children.FirstName
            }));

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

Per l'esempio completo fare clic [qui][qui].

## <span id="NextSteps"></span></a>Passaggi successivi

-   Informazioni su come [monitorare un account DocumentDB][monitorare un account DocumentDB].
-   Per dettagli sul modello di programmazione, vedere la sezione Sviluppo nella [pagina della documentazione di DocumentDB][pagina della documentazione di DocumentDB]

  [Creare un account DocumentDB]: ../documentdb-create-account/
  [Connettersi a un account DocumentDB]: #Connect
  [Creare un database]: #CreateDB
  [Creare una raccolta]: #CreateColl
  [Creare i documenti]: #CreateDoc
  [Eseguire query sulle risorse di DocumentDB]: #Query
  [Passaggi successivi]: #NextSteps
  [0]: ./media/documentdb-get-started/gs1.png
  [come funzionano le stringhe di applicazione e di connessione in Siti Web di Microsoft Azure]: http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/
  [qui]: https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started
  [monitorare un account DocumentDB]: http://go.microsoft.com/fwlink/p/?LinkId=402378
  [pagina della documentazione di DocumentDB]: http://go.microsoft.com/fwlink/p/?LinkID=402319
