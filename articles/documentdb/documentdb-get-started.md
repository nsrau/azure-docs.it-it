<properties
	pageTitle="Introduzione a DocumentDB .NET SDK | Microsoft Azure"
	description="Informazioni su come creare e configurare un account DocumentDB, creare database, creare raccolte e archiviare documenti JSON nell'account del database di documenti NoSQL."
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
	ms.date="09/03/2015"
	ms.author="anhoh"/>

#Introduzione a DocumentDB .NET SDK  

Questa esercitazione illustra come iniziare a usare [Microsoft Azure DocumentDB](https://azure.microsoft.com/services/documentdb/) e [DocumentDB .NET SDK](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/). È possibile compilare un'applicazione console che crea ed esegue una query sulle risorse DocumentDB e scrive l'output nella finestra della console.

DocumentDB è un servizio di database di documenti NoSQL per il quale sono disponibili una [serie di API e SDK](https://msdn.microsoft.com/library/dn781482.aspx). Il codice riportato in questo articolo è scritto in C# ed è basato su DocumentDB .NET SDK, che è distribuito come pacchetto NuGet.

In questo articolo vengono trattati i seguenti scenari:

- Creazione e connessione a un account DocumentDB
- Aggiunta di DocumentDB alla soluzione Visual Studio
- Creazione di database
- Creazione di raccolte
- Creazione di documenti JSON
- Esecuzione di query su risorse
- Eliminazione di database

Se non si ha tempo di completare l'esercitazione e si preferisce ottenere semplicemente la soluzione efficace, non c'è nessun problema. La soluzione completa è disponibile in [GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started). Per istruzioni rapide, vedere [ottenere la soluzione completa](#GetSolution).

Dopo aver completato l'esercitazione, utilizzando i pulsanti di voto all'inizio o alla fine dell'argomento per farci conoscere il giudizio. Questo argomento viene aggiornato attivamente, pertanto desideriamo il tuo feedback per migliorarlo. Se si desidera contattarsi, è possibile includere l'indirizzo di posta elettronica nel commento per il follow-up.

## Prerequisiti

Prima di seguire le istruzioni di questo articolo, verificare che siano disponibili gli elementi seguenti:

- Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/).
- [Visual Studio 2013](http://www.visualstudio.com/) Update 4 o versioni successive.

## Passaggio 1: Creare un account DocumentDB

Il primo passaggio consiste nella creazione di un account DocumentDB. Se si dispone già di un account, è possibile ignorare [Configurare una soluzione Visual Studio](#SetupVS).

[AZURE.INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

##<a id="SetupVS"></a> Passaggio 2: Configurare una soluzione Visual Studio

1. Aprire **Visual Studio** nel computer.
2. Selezionare **Nuovo** dal menu **File** e scegliere **Progetto**.
3. Nella **finestra di dialogo Nuovo progetto**, selezionare **Modelli** / **Visual C#** / **Applicazione console**, assegnare un nome al progetto e quindi fare clic su **Aggiungi**.
4. In **Esplora soluzioni**, fare clic sulla nuova applicazione console che si trova nella soluzione di Visual Studio.
5. Senza lasciare il menu fare clic su **Gestisci pacchetti NuGet...**
6. Nel riquadro sinistro della finestra **Gestisci pacchetti NuGet** fare clic su **Online** / **nuget.org**.
7. Nella casella di input **Cerca Online** cercare **Libreria client di DocumentDB**.
8. Individuare tra i risultati della ricerca la **libreria client di Microsoft Azure DocumentDB** e fare clic su **Installa**. L'ID del pacchetto per la libreria client di DocumentDB è [Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB)

L'installazione è riuscita. A questo punto si è pronti per iniziare a usare DocumentDB.

##<a id="Connect"></a> Passaggio 3: Connettersi a un account DocumentDB

Si inizia creando una nuova istanza della classe [DocumentClient](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.aspx) per stabilire una connessione all'account DocumentDB. All'inizio dell'applicazione C# sono necessari i seguenti riferimenti:

    using Microsoft.Azure.Documents;
    using Microsoft.Azure.Documents.Client;
    using Microsoft.Azure.Documents.Linq;
    using Newtonsoft.Json;

Un'istanza di **DocumentClient** può essere quindi creata usando l'endpoint dell'account DocumentDB e la chiave di accesso primaria o secondaria associata all'account. Aggiungere queste proprietà alla classe.

    private static string EndpointUrl = "<your endpoint URI>";
    private static string AuthorizationKey = "<your key>";

Creare a questo punto una nuova attività asincrona denominata **GetStartedDemo** nella classe. In questa nuova attività, creare e configurare il **DocumentClient**.

	private static async Task GetStartedDemo()
    {
		// Create a new instance of the DocumentClient.
    	var client = new DocumentClient(new Uri(EndpointUrl), AuthorizationKey);
	}

Chiamare l'attività asincrona dal metodo Main simile al codice riportato di seguito.

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

> [AZURE.WARNING]Non archiviare mai le credenziali nel codice sorgente. Per semplicità, in questo esempio verranno visualizzate nel codice sorgente. Per informazioni su come archiviare le credenziali in un ambiente di produzione, vedere i [siti Web di Microsoft Azure sul funzionamento delle stringhe di applicazione e di connessione](https://azure.microsoft.com/blog/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work/). Si osservi l'applicazione di esempio [GitHub](https://github.com/Azure/azure-documentdb-net/blob/master/tutorials/get-started/src/Program.cs) per un esempio di memorizzazione delle credenziali di fuori del codice sorgente.

I valori per EndpointUrl e AuthorizationKey sono l'URI e la CHIAVE PRIMARIA per l'account DocumentDB, che possono essere ottenuti dal pannello [Chiavi](https://portal.azure.com) per l'account DocumentDB.

![Schermata del portale di anteprima di Azure, che mostra un account DocumentDB, con l'hub ACTIVE evidenziato, il pulsante CHIAVI evidenziato nel pannello dell'account DocumentDB e i valori di URI, CHIAVE PRIMARIA e CHIAVE SECONDARIA evidenziati nel pannello Chiavi][keys]

Queste chiavi concedono l'accesso amministrativo all'account DocumentDB e alle relative risorse. DocumentDB supporta anche l'uso delle chiavi di risorsa che consentono ai client di leggere, scrivere ed eliminare le risorse nell'account DocumentDB in base alle autorizzazioni concesse, senza bisogno di una chiave di account. Per altre informazioni sulle chiavi delle risorse, vedere [Autorizzazioni](documentdb-resources.md#permissions) e [Visualizzare, copiare e rigenerare le chiavi di accesso](documentdb-manage-account.md#keys).

Dopo aver appreso come connettersi a un account DocumentDB e creare un'istanza della classe **DocumentClient**, è possibile esaminare la modalità d'uso delle risorse di DocumentDB.

## Passaggio 4: Creare un database
È possibile creare un [database](documentdb-resources.md#databases) usando il metodo [CreateDatabaseAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdatabaseasync.aspx) della classe **DocumentClient**. Un database è un contenitore logico di archiviazione documenti partizionato nelle raccolte. Creare il nuovo database nel metodo **GetStartedDemo** dopo la creazione di **DocumentClient**.

	// Create a database.
	Database database = await client.CreateDatabaseAsync(
		new Database
		    {
			    Id = "FamilyRegistry"
		    });

##<a id="CreateColl"></a>Passaggio 5: Creare una raccolta  

> [AZURE.WARNING]**CreateDocumentCollectionAsync** creerà una nuova raccolta S1, che ha implicazioni in termini di prezzi. Per altre informazioni, visitare la [pagina relativa ai prezzi](https://azure.microsoft.com/pricing/details/documentdb/).

È possibile creare una [raccolta](documentdb-resources.md#collections) usando il metodo [CreateDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentcollectionasync.aspx) della classe **DocumentClient**. Una raccolta è un contenitore di documenti JSON e di logica dell'applicazione JavaScript associata. La raccolta appena creata verrà mappata a un [livello di prestazioni S1](documentdb-performance-levels.md). Il database creato nel passaggio precedente ha diverse proprietà, una delle quali è [CollectionsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.database.collectionslink.aspx). Con queste informazioni ora è possibile creare una raccolta dopo la creazione del database.

  	// Create a document collection.
  	DocumentCollection documentCollection = await client.CreateDocumentCollectionAsync(database.CollectionsLink,
  		new DocumentCollection
  		    {
  			    Id = "FamilyCollection"
  		    });

##<a id="CreateDoc"></a>Passaggio 6: Creare documenti
È possibile creare un [documento](documentdb-resources.md#documents) usando il metodo [CreateDocumentAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.createdocumentasync.aspx) della classe **DocumentClient**. I documenti sono contenuto JSON definito dall'utente (arbitrario). La raccolta creata nel passaggio precedente ha diverse proprietà, una delle quali è [DocumentsLink](https://msdn.microsoft.com/library/microsoft.azure.documents.documentcollection.documentslink.aspx). Con queste informazioni ora è possibile inserire uno o più documenti. Se sono già disponibili dati da archiviare nel database, è possibile usare lo [strumento di migrazione dei dati](documentdb-import-data.md) di DocumentDB.

Prima di tutto è necessario creare una classe **Parent**, **Child**, **Pet**, **Address** e **Family**. Creare queste classi aggiungendo le classi secondarie interne seguenti.

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

A questo punto sono stati creati il database, la raccolta e i documenti seguenti nell'account di DocumentDB.

![Diagramma che illustra la relazione gerarchica tra l'account, il database, la raccolta e i documenti](./media/documentdb-get-started/account-database.png)

##<a id="Query"></a>Passaggio 7: Eseguire query sulle risorse di DocumentDB

DocumentDB supporta [query](documentdb-sql-query.md) complesse sui documenti JSON archiviati in ogni raccolta. Il codice di esempio seguente mostra diverse query che usano la sintassi SQL di DocumentDB e LINQ e che è possibile eseguire sui documenti inseriti nel passaggio precedente. Aggiungere queste query al metodo asincrono **GetStartedDemo**.

    // Query the documents using DocumentDB SQL for the Andersen family.
    var families = client.CreateDocumentQuery(documentCollection.DocumentsLink,
        "SELECT * " +
        "FROM Families f " +
        "WHERE f.id = "AndersenFamily"");

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

Nel diagramma seguente viene illustrato come la sintassi di query di SQL DocumentDB viene chiamata sulla raccolta creata e la stessa logica viene applicata alla query di LINQ.

![Diagramma che illustra l'ambito e il significato della query](./media/documentdb-get-started/collection-documents.png)

La parola chiave [FROM](documentdb-sql-query.md/#from-clause) è facoltativa nella query perché le query di DocumentDB sono già limitate a una singola raccolta. Di conseguenza, "FROM Families f" può essere scambiata con "FROM root r" o con il nome di qualsiasi altra variabile scelta. DocumentDB dedurrà che Families, root o il nome della variabile scelta, si riferisce per impostazione predefinita alla raccolta attuale.

##<a id="DeleteDatabase"></a>Passaggio 8: Eliminare il database

Se si elimina il database creato, verrà rimosso il database e tutte le risorse figlio (raccolte, documenti e così via). È possibile eliminare il database e il client di documenti aggiungendo il frammento di codice seguente alla fine del metodo asincrono **GetStartedDemo**.

    // Clean up/delete the database
    await client.DeleteDatabaseAsync(database.SelfLink);
	client.Dispose();

##<a id="Run"></a>Passaggio 9: Eseguire l'applicazione

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
	{
	  "id": "AndersenFamily",
 	  "child": "Henriette Thaulow"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Jesse"
	}
	{
	  "id": "WakefieldFamily",
	  "child": "Lisa"
	}
	{ family = AndersenFamily, child = Henriette Thaulow }
	{ family = WakefieldFamily, child = Jesse }
	{ family = WakefieldFamily, child = Lisa }


> [AZURE.NOTE]Se si esegue l'applicazione più volte senza rimuovere il database, è possibile si verifichi un problema di creazione di un nuovo database con un id già in uso. Per evitare questo problema, è possibile verificare se esiste già un database, una raccolta o un documento con lo stesso id. Per informazioni a questo proposito, visitare la [pagina GitHub](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started).

Congratulazioni! È stata creata la prima applicazione DocumentDB!

##<a id="GetSolution"></a> Ottenere la soluzione completa
Per creare la soluzione GetStarted completa contenente tutti gli esempi riportati in questo articolo, è necessario avere:

-   [Account DocumentDB][documentdb-create-account].
-   La soluzione [GetStarted](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/get-started) disponibile su GitHub.

Per ripristinare i riferimenti a DocumentDB .NET SDK in Visual Studio 2013, fare clic con il pulsante destro del mouse sulla soluzione **GetStarted** in Esplora soluzioni e quindi fare clic sul **comando per abilitare il ripristino del pacchetto NuGet**. Nel file App.config aggiornare quindi i valori EndpointUrl e AuthorizationKey come illustrato nell'articolo [Connettersi a un account DocumentDB](#Connect).

## Passaggi successivi

-   Si preferisce un esempio più complesso di ASP.NET MVC? Vedere [Creare un'applicazione Web con MVC ASP.NET usando DocumentDB](documentdb-dotnet-application.md).
-	Informazioni su come [monitorare un account DocumentDB](documentdb-monitor-accounts.md).
-	Eseguire query sul set di dati di esempio illustrato nella pagina [Query Playground](https://www.documentdb.com/sql/demo).
-	Per informazioni dettagliate sul modello di programmazione, vedere la sezione Sviluppo nella pagina relativa alla [documentazione di DocumentDB](../../services/documentdb/).

[doc-landing-page]: ../../services/documentdb/
[documentdb-create-account]: documentdb-create-account.md
[documentdb-manage]: documentdb-manage.md

[keys]: media/documentdb-get-started/keys.png
 

<!---HONumber=September15_HO1-->