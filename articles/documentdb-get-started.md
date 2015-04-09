<properties 
	pageTitle="Introduzione a DocumentDB .NET SDK | Azure" 
	description="Informazioni su come creare e configurare un account DocumentDB, creare database, creare raccolte e archiviare documenti JSON nell'account del database di documenti NoSQL." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/23/2015" 
	ms.author="anhoh"/>

#Introduzione a DocumentDB .NET SDK  

Questa guida illustra come usare [Microsoft Azure DocumentDB (Anteprima)](https://portal.azure.com/#gallery/Microsoft.DocumentDB) e [DocumentDB .NET SDK](https://go.microsoft.com/fwlink/p/?linkid=402989). DocumentDB è un servizio di database di documenti NoSQL per il quale [sono disponibili API e SDK](https://go.microsoft.com/fwlink/p/?linkid=522476). Gli esempi di codice riportati in questo articolo sono scritti in C# e sono basati su DocumentDB .NET SDK, che è distribuito come pacchetto NuGet. 

Gli scenari presentati in questo articolo includono la creazione e la configurazione di un account DocumentDB, la creazione di database, la creazione di raccolte e l'archiviazione di documenti JSON nell'account. Ciascuno di questi esempi è parte di una soluzione completa disponibile su [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). È possibile [scaricare la soluzione](#GetSolution) per visualizzare il codice di esempio nel contesto oppure è possibile semplicemente esaminare gli esempi in questo articolo.

##<a id="CreateAccount"></a>Creare un account DocumentDB

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

##<a id="Connect"></a>Connettersi a un account DocumentDB

Si inizia creando una nuova istanza della classe [DocumentClient](https://go.microsoft.com/fwlink/p/?linkid=522477) per stabilire una connessione all'account DocumentDB. Nell'applicazione C# sono necessari i riferimenti seguenti:  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
L'istanza di **DocumentClient** può essere quindi creata usando l'endpoint dell'account DocumentDB e la chiave di accesso primaria o secondaria associata all'account. Creare il client usando codice analogo a quello dell'esempio seguente:   

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

> [AZURE.WARNING] Non archiviare mai le credenziali nel codice sorgente. Per semplicità, in questo esempio verranno visualizzate nel codice sorgente. Vedere la sezione che descrive [come funzionano le stringhe di applicazione e di connessione in Siti Web di Microsoft Azure](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/) per informazioni su come archiviare le credenziali in un ambiente di produzione. 

I valori per EndpointUrl e AuthorizationKey sono l'URI e la CHIAVE PRIMARIA per l'account DocumentDB, che possono essere ottenuti dal pannello del [portale di gestione in anteprima di Azure](https://portal.azure.com) per l'account DocumentDB. 

![Screen shot of the Azure Preview portal, showing a DocumentDB account, with the ACTIVE hub highlighted, the KEYS button highlighted on the DocumentDB account blade, and the URI, PRIMARY KEY and SECONDARY KEY values highlighted on the Keys blade][1]
 
Queste chiavi concedono l'accesso amministrativo all'account DocumentDB e alle relative risorse. DocumentDB supporta anche l'uso delle chiavi di risorsa che consentono ai client di leggere, scrivere ed eliminare le risorse nell'account DocumentDB in base alle autorizzazioni concesse, senza bisogno di una chiave di account. Per altre informazioni sulle chiavi delle risorse, vedere la sezione relativa alle autorizzazioni dell'articolo [Modello di risorse e concetti relativi a DocumentDB](documentdb-resources.md).

Dopo aver appreso come connettersi a un account DocumentDB e creare un'istanza della classe **DocumentClient**, è possibile esaminare la modalità d'uso delle risorse di DocumentDB.  

##<a id="CreateDB"></a>Creare un database
È possibile creare un database usando il metodo [CreateDatabaseAsync](https://go.microsoft.com/fwlink/p/?linkid=522478) della classe **DocumentClient**.  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Creare una raccolta  

È possibile creare una raccolta usando il metodo [CreateDocumentCollectionAsync](https://go.microsoft.com/fwlink/p/?linkid=522479) della classe **DocumentClient**.  Il database creato nel passaggio precedente ha diverse proprietà, una delle quali è [CollectionsLink](https://go.microsoft.com/fwlink/p/?linkid=522481).  Con queste informazioni ora è possibile creare una raccolta.  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
##<a id="CreateDoc"></a>Creare i documenti	
È possibile creare un documento usando il metodo [CreateDocumentAsync](https://go.microsoft.com/fwlink/p/?linkid=522482) della classe **DocumentClient**.  La raccolta creata nel passaggio precedente ha diverse proprietà, una delle quali è [DocumentsLink](https://go.microsoft.com/fwlink/p/?linkid=522483).  Con queste informazioni ora è possibile inserire uno o più documenti.  Ai fini di questo esempio, si presuppone l'esistenza di una classe Family che descrive gli attributi di una famiglia, ad esempio nome, genere ed età. Per visualizzare la classe Family di esempio, vedere il [repository GitHub](https://github.com/Azure/azure-documentdb-net/blob/master/tutorials/get-started/src/Program.cs). 

    // Create the Andersen family document.
	Family AndersenFamily = new Family
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, AndersenFamily);
    
    // Create the WakeField family document.
    Family WakefieldFamily = new Family
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

    await client.CreateDocumentAsync(documentCollection.DocumentsLink, WakefieldFamily);
 

##<a id="Query"></a>Eseguire query sulle risorse di DocumentDB

DocumentDB supporta le query complesse sui documenti JSON archiviati in ciascuna raccolta.  Il codice di esempio seguente mostra diverse query che usano la sintassi SQL di DocumentDB e LINQ e che è possibile eseguire sui documenti inseriti nel passaggio precedente. 

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = \"AndersenFamily\"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery(documentCollection.DocumentsLink)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery(documentCollection.DocumentsLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    // Query the documents using DocumentSQL with one join.
    var items = client.CreateDocumentQuery<dynamic>(documentCollection.DocumentsLink,
        "SELECT f.id, c.FirstName AS child " +
        "FROM Families f " +
        "JOIN c IN f.Children");

    foreach (var item in items.ToList())
    {
        Console.WriteLine(item);
    }

    // Query the documents using LINQ with one join.
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
	
##<a id="GetSolution"></a>Ottenere la soluzione completa
Per creare la soluzione GetStarted completa contenente tutti gli esempi riportati in questo articolo, è necessario avere:

-   [Account DocumentDB][documentdb-create-account].
-   La soluzione [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) disponibile su GitHub. 

Per ripristinare i riferimenti a DocumentDB .NET SDK in Visual Studio 2013, fare clic con il pulsante destro del mouse sulla soluzione GetStarted in Esplora soluzioni, quindi scegliere il comando per abilitare il ripristino del pacchetto NuGet. Nel file App.config aggiornare quindi i valori EndpointUrl e AuthorizationKey come illustrato nell'articolo [Connettersi a un account DocumentDB](#Connect). 

##<a id="NextSteps"></a>Passaggi successivi
-	Informazioni su come [monitorare un account DocumentDB](https://go.microsoft.com/fwlink/p/?LinkId=402378).
-	Eseguire query sul set di dati di esempio illustrato nella pagina [Query Playground](http://www.documentdb.com/sql/demo).
-	Per informazioni dettagliate sul modello di programmazione, vedere la sezione Sviluppo nella [pagina della documentazione di DocumentDB](../documentation/services/documentdb/).


[Connettersi a un account DocumentDB]: #Connect
[Creare un database]: #CreateDB
[Creare una raccolta]: #CreateColl
[Creare i documenti]: #CreateDoc
[Eseguire query sulle risorse di DocumentDB]: #Query
[Passaggi successivi]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[1]: ../includes/media/documentdb-keys/keys.png


<!--HONumber=49-->