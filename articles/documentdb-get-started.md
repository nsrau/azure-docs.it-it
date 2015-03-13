<properties 
	pageTitle="Introduzione a DocumentDB .NET SDK | Azure" 
	description="Informazioni su come creare e configurare un account DocumentDB, creare database, creare raccolte e archiviare documenti JSON nell'account del database di documenti NoSQL." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/12/2014" 
	ms.author="mimig"/>

#Introduzione a DocumentDB .NET SDK  

Questa guida illustra come usare [**Microsoft Azure DocumentDB (Preview)**](https://portal.azure.com/#gallery/Microsoft.DocumentDB) e DocumentDB .NET SDK. DocumentDB è un servizio di database di documenti NoSQL per il quale sono disponibili API e SDK. Gli esempi di codice riportati in questo articolo sono scritti in C# e sono basati su [DocumentDB .NET SDK](http://go.microsoft.com/fwlink/p/?linkid=402989), che è distribuito come pacchetto NuGet. 

Gli scenari presentati in questo articolo includono la creazione e la configurazione di un account DocumentDB, la creazione di database, la creazione di raccolte e l'archiviazione di documenti JSON nell'account. Ciascuno di questi esempi è parte di una soluzione completa disponibile su [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). È possibile [scaricare la soluzione](#GetSolution) per visualizzare il codice di esempio nel contesto oppure è possibile semplicemente esaminare gli esempi in questo articolo.

##Sommario

-	[Connettersi a un account DocumentDB](#Connect)
-	[Creare un database](#CreateDB)
-	[Creare una raccolta](#CreateColl)
-	[Creare i documenti](#CreateDoc)
-	[Eseguire query sulle risorse di DocumentDB](#Query)
-	[Ottenere la soluzione completa](#GetSolution)
-	[Passaggi successivi](#NextSteps)

##<a id="Connect"></a>Connettersi a un account DocumentDB

Si inizia creando una nuova istanza della classe **DocumentClient** per stabilire una connessione all'account DocumentDB.   Nell'applicazione C# sono necessari i riferimenti seguenti:  

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
 
L'istanza di **DocumentClient** può essere creata usando l'endpoint dell'account DocumentDB e la chiave di accesso primaria o secondaria associata all'account.  

L'endpoint e le chiavi dell'account DocumentDB sono disponibili nel pannello del [portale di gestione di anteprima di Azure](https://portal.azure.com/#gallery/Microsoft.DocumentDB) per l'account DocumentDB. 

![][1]
 
>Tenere presente che le chiavi di accesso di DocumentDB disponibili mediante il pannello delle chiavi consentono l'accesso amministrativo all'account DocumentDB e alle risorse in esso contenute.  DocumentDB supporta anche l'uso delle chiavi di risorsa che consentono ai client di leggere, scrivere ed eliminare le risorse nell'account DocumentDB in base alle autorizzazioni concesse, senza bisogno di una chiave di account.    

Creare un client usando un codice come quello contenuto nell'esempio seguente.  

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";
    
    // Create a new instance of the DocumentClient.
    var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);  

**Avviso:** anche se è opportuno evitare sempre di archiviare le credenziali nel codice sorgente, per semplicità in questo esempio le credenziali sono visibili nel codice sorgente. Per informazioni su come archiviare le credenziali, vedere l'articolo relativo alla [modalità di funzionamento delle stringhe di applicazione e di connessione in Siti Web di Microsoft Azure](http://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). 

Dopo aver appreso come connettersi a un account DocumentDB e creare un'istanza di **DocumentClient**, è possibile esaminare la modalità d'uso delle risorse di DocumentDB.  

##<a id="CreateDB"></a>Creare un database
Con DocumentDB .NET SDK è possibile creare un database di DocumentDB usando il metodo **CreateDatabaseAsync** di DocumentClient.  

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Creare una raccolta  

Con DocumentDB .NET SDK è possibile creare una raccolta di DocumentDB usando il metodo **CreateDocumentCollectionAsync** di DocumentClient.  Il database creato nel passaggio precedente ha diverse proprietà, una delle quali è **CollectionsLink**.  Con queste informazioni ora è possibile creare una raccolta.  

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });
    
##<a id="CreateDoc"></a>Creare i documenti	
Con DocumentDB .NET SDK è possibile creare un documento di DocumentDB usando il metodo **CreateDocumentAsync** di DocumentClient.  La raccolta creata nel passaggio precedente ha diverse proprietà, una delle quali è **DocumentsLink**.  Con queste informazioni ora è possibile inserire uno o più documenti.  Ai fini di questo esempio, si presuppone l'esistenza di una classe Family che descrive gli attributi di una famiglia, ad esempio nome, genere ed età.  

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

-   [Un account DocumentDB][documentdb-create-account]
-   La soluzione [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) disponibile su GitHub 

Per ripristinare i riferimenti a DocumentDB .NET SDK in Visual Studio 2013, fare clic con il pulsante destro del mouse sulla soluzione GetStarted in Esplora soluzioni, quindi scegliere il comando per abilitare il ripristino del pacchetto NuGet. 

##<a id="NextSteps"></a>Passaggi successivi
-	Informazioni su come [monitorare un account DocumentDB](http://go.microsoft.com/fwlink/p/?LinkId=402378).
-	Per informazioni dettagliate sul modello di programmazione, vedere la sezione Sviluppo nella [pagina della documentazione di DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).


[Connettersi a un account DocumentDB]: #Connect
[Creare un database]: #CreateDB
[Creare una raccolta]: #CreateColl
[Creare i documenti]: #CreateDoc
[Eseguire query sulle risorse di DocumentDB]: #Query
[Passaggi successivi]: #NextSteps
[doc-landing-page]: ../documentation/services/documentdb/
[documentdb-create-account]: ../documentdb-create-account/
[documentdb-manage]: ../documentdb-manage/

[1]: ./media/documentdb-get-started/gs1.png

<!--HONumber=35.2-->

<!--HONumber=46--> 
