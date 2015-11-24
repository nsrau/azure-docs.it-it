<properties
	pageTitle="Esercitazione su NoSQL: DocumentDB .NET SDK | Microsoft Azure"
	description="Esercitazione su NoSQL che crea un database online e un'applicazione console in C# con DocumentDB .NET SDK. DocumentDB è un database NoSQL per JSON."
	keywords="esercitazione su nosql, database online, applicazione console C#"
	services="documentdb"
	documentationCenter=".net"
	authors="AndrewHoh"
	manager="jhubbard"
	editor="monicar"/>

<tags
	ms.service="documentdb"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="11/18/2015"
	ms.author="anhoh"/>

# Esercitazione su NoSQL: applicazione console C# di DocumentDB 

> [AZURE.SELECTOR]
- [.NET](documentdb-get-started.md)
- [Node.js](documentdb-nodejs-get-started.md)

Esercitazione su NoSQL per DocumentDB .NET SDK Dopo aver seguito questa esercitazione, si otterrà un'applicazione console che consente di creare e ridefinire le query delle risorse DocumentDB.

Tratteremo questo argomento:

- Creazione e connessione a un account DocumentDB
- Configurare una soluzione Visual Studio
- Creazione di un database online
- Creare una raccolta
- Creazione di documenti JSON
- Esecuzione di query sulla raccolta
- Eliminazione del database

Non si ha tempo? Nessun problema. La soluzione completa è disponibile in [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started). Per istruzioni rapide, vedere [ottenere la soluzione completa](#GetSolution).

Successivamente, utilizzare i pulsanti di voti all'inizio o alla fine di questa pagina per fornire feedback. Se si desidera contattarci, è possibile includere l'indirizzo di posta elettronica nel commento per il follow-up.

Ecco come procedere.

## Prerequisiti

Assicurarsi di disporre di quanto segue:

- Un account Azure attivo. Se non si ha un account, è possibile iscriversi per ottenere una [versione di valutazione gratuita](http://azure.microsoft.com/pricing/free-trial/).
- [Visual Studio 2013/Visual Studio 2015](http://www.visualstudio.com/).

## Passaggio 1: Creare un account DocumentDB

Creare un account DocumentDB. Se si ha già un account, è possibile ignorare questo passaggio e passare a [Configurare la soluzione Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Passaggio 2: Configurare la soluzione di Visual Studio

1. Aprire **Visual Studio** nel computer.
2. Scegliere **Nuovo** dal menu **File** e quindi selezionare **Progetto**.
3. Nella finestra di dialogo **Nuovo progetto** selezionare **Modelli** / **Visual C#** / **Applicazione console**, assegnare un nome al progetto e quindi fare clic su **OK**.
4. In **Esplora soluzioni**, fare clic sulla nuova applicazione console che si trova nella soluzione di Visual Studio.
5. Senza lasciare il menu fare clic su **Gestisci pacchetti NuGet...**
6. Nel riquadro sinistro della finestra **Gestisci pacchetti NuGet** fare clic su **Online** / **nuget.org**.
7. Nella casella di input **Cerca Online** cercare **Libreria client di DocumentDB**.
8. Individuare tra i risultati della ricerca la **libreria client di Microsoft Azure DocumentDB** e fare clic su **Installa**. L'ID del pacchetto per la libreria client di DocumentDB è [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)

L'installazione è riuscita. Ora che abbiamo completato l'installazione, iniziamo a scrivere il codice.

##<a id="Connect"></a> Passaggio 3: Connettersi a un account DocumentDB

In primo luogo, aggiungere questi riferimenti all'inizio dell'applicazione c#, nel file Program.cs:

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

> [AZURE.IMPORTANT]Per completare questa esercitazione su NoSQL, assicurarsi di aggiungere le dipendenze illustrate sopra.

Successivamente, salvare l'endpoint dell'account DocumentDB e la chiave di accesso primaria o secondaria, disponibili nel [portale di anteprima di Azure](https://portal.azure.com).

![Schermata del portale di anteprima di Azure, che mostra un account DocumentDB, con l'hub ACTIVE evidenziato, il pulsante CHIAVI evidenziato nel pannello dell'account DocumentDB e i valori di URI, CHIAVE PRIMARIA e CHIAVE SECONDARIA evidenziati nel pannello Chiavi][keys]

    private const string EndpointUrl = "<your endpoint URI>";
    private const string AuthorizationKey = "<your key>";

Per avviare l'applicazione demo della guida introduttiva è necessario creare una nuova istanza di **DocumentClient**. Quindi, creare una nuova attività asincrona denominata **GetStartedDemo** e creare un'istanza del nuovo oggetto **DocumentClient**.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

Infine, chiamare l'attività asincrona dal metodo **Main** simile al codice seguente.

	public static void Main(string[] args)
    {
		try
    	{
        	GetStartedDemo().Wait();
		}
		catch (Exception e)
		{
			Exception baseException = e.GetBaseException();
			Console.WriteLine("Error: {0}, Message: {1}", e.Message, baseException.Message);
		}
	}

> [AZURE.WARNING]Non archiviare mai le credenziali nel codice sorgente. Per semplicità, in questo esempio verranno visualizzate nel codice sorgente. Per informazioni su come archiviare le credenziali in un ambiente di produzione, vedere i [siti Web di Microsoft Azure sul funzionamento delle stringhe di applicazione e di connessione](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Si osservi l'applicazione di esempio [GitHub](https://github.com/Azure-Samples/documentdb-dotnet-getting-started/blob/master/src/Program.cs) per un esempio di memorizzazione delle credenziali di fuori del codice sorgente.

Dopo aver appreso come connettersi a un account DocumentDB e creare un'istanza della classe **DocumentClient**, è possibile esaminare la modalità d'uso delle risorse di DocumentDB.

## Passaggio 4: Creare un database online
È possibile creare un [database](documentdb-resources.md#databases) di DocumentDB usando il metodo [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) della classe **DocumentClient**. Un database è il contenitore logico per l'archiviazione di documenti JSON partizionato nelle raccolte. Creare il nuovo database nel metodo **GetStartedDemo** dopo la creazione di **DocumentClient**.

	// Check to verify a database with the id=FamilyRegistry does not exist
	Database database = client.CreateDatabaseQuery().Where(db => db.Id == "FamilyRegistry").AsEnumerable().FirstOrDefault();

	// If the database does not exist, create a new database
    if (database == null)
    {
    	database = await client.CreateDatabaseAsync(
    		new Database
            {
            	Id = "FamilyRegistry"
            });

		// Write the new database's id to the console
		Console.WriteLine(database.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateColl"></a>Passaggio 5: Creare una raccolta  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** creerà una nuova raccolta S1, che ha implicazioni in termini di prezzi. Per altre informazioni, visitare la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/documentdb/).

È possibile creare una [raccolta](documentdb-resources.md#collections) usando il metodo [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) della classe **DocumentClient**. Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata. La raccolta appena creata verrà mappata a un [livello di prestazioni S1](documentdb-performance-levels.md). Creare una nuova raccolta denominata **FamilyCollection** dopo la creazione del database nel metodo **GetStartedDemo**.

    // Check to verify a document collection with the id=FamilyCollection does not exist
    DocumentCollection documentCollection = client.CreateDocumentCollectionQuery("dbs/" + database.Id).Where(c => c.Id == "FamilyCollection").AsEnumerable().FirstOrDefault();

	// If the document collection does not exist, create a new collection
    if (documentCollection == null)
    {
    	documentCollection = await client.CreateDocumentCollectionAsync("dbs/" + database.Id,
        	new DocumentCollection
            {
            	Id = "FamilyCollection"
            });

		// Write the new collection's id to the console
		Console.WriteLine(documentCollection.Id);
        Console.WriteLine("Press any key to continue ...");
        Console.ReadKey();
        Console.Clear();
	}

##<a id="CreateDoc"></a>Passaggio 6: Creare documenti JSON
È possibile creare un [documento](documentdb-resources.md#documents) usando il metodo [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) della classe **DocumentClient**. I documenti sono contenuto JSON definito dall'utente (arbitrario). Ora è possibile inserire uno o più documenti. Se sono già disponibili dati da archiviare nel database, è possibile usare lo [strumento di migrazione dei dati](documentdb-import-data.md) di DocumentDB.

Prima di tutto è necessario creare una classe **Parent**, **Child**, **Pet**, **Address** e **Family**. Creare queste classi aggiungendo le classi secondarie interne seguenti dopo il metodo **GetStartedDemo**.

    internal sealed class Parent
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
    }

    internal sealed class Child
    {
        public string FamilyName { get; set; }
        public string FirstName { get; set; }
        public string Gender { get; set; }
        public int Grade { get; set; }
        public Pet[] Pets { get; set; }
    }

    internal sealed class Pet
    {
        public string GivenName { get; set; }
    }

    internal sealed class Address
    {
        public string State { get; set; }
        public string County { get; set; }
        public string City { get; set; }
    }

    internal sealed class Family
    {
        [JsonProperty(PropertyName = "id")]
        public string Id { get; set; }
        public string LastName { get; set; }
        public Parent[] Parents { get; set; }
        public Child[] Children { get; set; }
        public Address Address { get; set; }
        public bool IsRegistered { get; set; }
    }

Procedere creando i documenti all'interno del metodo asincrono **GetStartedDemo**.

    // Check to verify a document with the id=AndersenFamily does not exist
    Document document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "AndersenFamily").AsEnumerable().FirstOrDefault();

	// If the document does not exist, create a new document
	if (document == null)
	{
	    // Create the Andersen Family document
	    Family andersonFamily = new Family
	    {
	        Id = "AndersenFamily",
	        LastName = "Andersen",
	        Parents = new Parent[] {
	            new Parent { FirstName = "Thomas" },
	            new Parent { FirstName = "Mary Kay"}
	        },
	        Children = new Child[] {
	            new Child
	            { 
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
	
	    // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
	    await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, andersonFamily);
	}

    // Check to verify a document with the id=AndersenFamily does not exist
    document = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id).Where(d => d.Id == "WakefieldFamily").AsEnumerable().FirstOrDefault();

    if (document == null)
    {
        // Create the WakeField document
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

        // id based routing for the first argument, "dbs/FamilyRegistry/colls/FamilyCollection"
        await client.CreateDocumentAsync("dbs/" + database.Id + "/colls/" + documentCollection.Id, wakefieldFamily);
	}

A questo punto sono stati creati il database, la raccolta e i documenti seguenti nell'account di DocumentDB.

![Diagramma che illustra la relazione gerarchica tra l'account, il database, la raccolta e i documenti](./media/documentdb-get-started/nosql-tutorial-account-database.png)

##<a id="Query"></a>Passaggio 7: Eseguire query sulle risorse di DocumentDB

DocumentDB supporta [query](documentdb-sql-query.md) complesse sui documenti JSON archiviati in ogni raccolta. Il codice di esempio seguente mostra diverse query che usano la sintassi SQL di DocumentDB e LINQ e che è possibile eseguire sui documenti inseriti nel passaggio precedente. Aggiungere queste query al metodo asincrono **GetStartedDemo**.

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    // Query the documents using LINQ for the Andersen family.
    families =
        from f in client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        where f.Id == "AndersenFamily"
        select f;

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ", family);
    }

    // Query the documents using LINQ lambdas for the Andersen family.
    families = client.CreateDocumentQuery("dbs/" + database.Id + "/colls/" + documentCollection.Id)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f);

    foreach (var family in families)
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

Nel diagramma seguente viene illustrato come la sintassi di query di SQL DocumentDB viene chiamata sulla raccolta creata e la stessa logica viene applicata alla query di LINQ.

![Diagramma che illustra l'ambito e il significato della query](./media/documentdb-get-started/nosql-tutorial-collection-documents.png)

La parola chiave [FROM](documentdb-sql-query.md#from-clause) è facoltativa nella query perché le query di DocumentDB sono già limitate a una singola raccolta. Di conseguenza, "FROM Families f" può essere scambiata con "FROM root r" o con il nome di qualsiasi altra variabile scelta. DocumentDB dedurrà che Families, root o il nome della variabile scelta, si riferisce per impostazione predefinita alla raccolta attuale.

##<a id="DeleteDatabase"></a>Passaggio 8: Eliminare il database

Se si elimina il database creato, verrà rimosso il database e tutte le risorse figlio (raccolte, documenti e così via). È possibile eliminare il database e il client di documenti aggiungendo il frammento di codice seguente alla fine del metodo asincrono **GetStartedDemo**.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync("dbs/" + database.Id);
	client.Dispose();

##<a id="Run"></a>Passaggio 9: Eseguire l'applicazione console C#

È ora possibile eseguire l'applicazione. Alla fine del metodo **Main** aggiungere la riga di codice seguente, che consente di leggere l'output della console prima che l'applicazione termini l'esecuzione.

	Console.ReadLine();

Premere F5 in Visual Studio per compilare l'applicazione in modalità debug.

A questo punto si visualizzerà l'output dell'app di avvio. L'output visualizzerà i risultati delle query aggiunte e dovrà corrispondere al testo di esempio riportato di seguito.

	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
    	{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from SQL
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ
	Read {
	  "id": "AndersenFamily",
	  "LastName": "Andersen",
	  "Parents": [
		{
		  "FamilyName": null,
		  "FirstName": "Thomas"
		},
		{
		  "FamilyName": null,
		  "FirstName": "Mary Kay"
		}
	  ],
	  "Children": [
		{
		  "FamilyName": null,
		  "FirstName": "Henriette Thaulow",
		  "Gender": "female",
		  "Grade": 5,
		  "Pets": [
			{
			  "GivenName": "Fluffy"
			}
		  ]
		}
	  ],
	  "Address": {
		"State": "WA",
		"County": "King",
		"City": "Seattle"
	  },
	  "IsRegistered": true,
	  "_rid": "ybVlALUoqAEBAAAAAAAAAA==",
	  "_ts": 1428372205,
	  "_self": "dbs/ybVlAA==/colls/ybVlALUoqAE=/docs/ybVlALUoqAEBAAAAAAAAAA==/",
	  "_etag": ""0000400c-0000-0000-0000-55233aed0000"",
	  "_attachments": "attachments/"
	} from LINQ query

Congratulazioni. L'esercitazione su NoSQL è stata completata.

##<a id="GetSolution"></a> Ottenere la soluzione completa
Per creare la soluzione GetStarted completa contenente tutti gli esempi riportati in questo articolo, è necessario avere:

-   [Account DocumentDB][documentdb-create-account].
-   La soluzione [GetStarted](https://github.com/Azure-Samples/documentdb-dotnet-getting-started) disponibile su GitHub.

Per ripristinare i riferimenti a DocumentDB .NET SDK in Visual Studio, fare clic con il pulsante destro del mouse sulla soluzione **GetStarted** in Esplora soluzioni e quindi scegliere **Abilita ripristino dei pacchetti NuGet**. Nel file App.config aggiornare quindi i valori EndpointUrl e AuthorizationKey come illustrato nell'articolo [Connettersi a un account DocumentDB](#Connect).

## Passaggi successivi

-   Si preferisce un'esercitazione su NoSQL MVC ASP.NET più complessa? Vedere [Creare un'applicazione Web con MVC ASP.NET usando DocumentDB](documentdb-dotnet-application.md).
-	Informazioni su come [monitorare un account DocumentDB](documentdb-monitor-accounts.md).
-	Eseguire query sul set di dati di esempio illustrato nella pagina [Query Playground](https://www.documentdb.com/sql/demo).
-	Per informazioni dettagliate sul modello di programmazione, vedere la sezione Sviluppo nella pagina relativa alla [documentazione di DocumentDB](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/nosql-tutorial-keys.png
 

<!---HONumber=Nov15_HO4-->