<properties 
	pageTitle="Creare un&#39;applicazione Web con MVC ASP.NET usando DocumentDB | Azure" 
	description="Informazioni su come usare DocumentDB con .NET per creare un&#39;applicazione Web per un elenco azioni. È possibile archiviare dati e accedervi da un&#39;applicazione Web MCV ASP.NET ospitata in Siti Web di Azure." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="04/29/2015" 
	ms.author="ryancraw"/>

#<a name="_Toc395809351"></a>Creare un'applicazione Web con MVC ASP.NET usando DocumentDB

Per illustrare come sfruttare in modo efficiente Azure DocumentDB per archiviare ed eseguire query su documenti JSON, questo articolo include una procedura dettagliata end-to-end che mostra come creare un'applicazione Web per un elenco azioni usando Azure DocumentDB. Le attività verranno memorizzate come documenti JSON in Azure DocumentDB.

![Schermata dell'applicazione Web per un elenco di azioni creata in questa esercitazione](./media/documentdb-dotnet-application/image1.png)

La procedura guidata mostra come usare il servizio DocumentDB fornito da Azure per archiviare i dati e accedervi da un'applicazione Web MCV ASP.NET ospitata in Azure.

> [AZURE.TIP]Questa esercitazione presuppone già una certa esperienza nell'uso di MCV ASP.NET e di Siti Web di Azure. Se non si ha alcuna esperienza riguardo ad ASP.NET o agli [strumenti richiesti come prerequisiti](#_Toc395637760), è consigliabile scaricare il progetto completo dell'esercitazione [todo](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/todo) da [GitHub](https://github.com/Azure/azure-documentdb-net) e creare la soluzione usando le [istruzioni alla fine di questo articolo](#GetProject). Una volta creata la soluzione, è possibile leggere l'articolo per approfondire il codice nel contesto del progetto.

## <a name="_Toc395637760"></a>Prerequisiti

Prima di seguire le istruzioni di questo articolo, verificare che siano disponibili gli elementi seguenti:

- Un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](../../pricing/free-trial/).
- [Visual Studio 2013](http://www.visualstudio.com/) Update 4 o versioni successive.
- Azure SDK per .NET versione 2.5.1 o successiva, disponibile tramite [Installazione guidata piattaforma Web Microsoft][].

Tutte le schermate presenti in questo articolo sono state acquisite da Visual Studio 2013 con Update 4 applicato e Azure SDK per .NET versione 2.5.1. Se il sistema in uso è configurato con versioni diverse, è probabile che le schermate e le opzioni non siano interamente corrispondenti. Se si soddisfano i prerequisiti precedenti, la soluzione dovrebbe funzionare comunque.

## <a name="_Toc395637761"></a>Passaggio 1: Creare un account di database di DocumentDB

Il primo passaggio consiste nella creazione di un account DocumentDB. Se si dispone già di un account, è possibile passare alla sezione [Creare una nuova applicazione MVC ASP.NET](#_Toc395637762).

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

<br/> Verrà ora illustrata in modo dettagliato la procedura per creare un'applicazione MVC ASP.NET completamente nuova.

## <a name="_Toc395637762"></a>Passaggio 2: Creare una nuova applicazione MVC ASP.NET

A questo punto, dopo aver creato un account, creare il nuovo progetto ASP.NET.

1. Dal menu **File** di Visual Studio scegliere **Nuovo** e quindi fare clic su **Progetto**.

   Verrà visualizzata la finestra di dialogo **Nuovo progetto**. 
2. Nel riquadro **Tipi progetto** espandere **Modelli**, **Visual C\#**, **Web** e quindi selezionare **Applicazione Web ASP.NET**.

  ![Schermata della finestra di dialogo Nuovo progetto con il tipo di progetto applicazione Web ASP.NET evidenziato](./media/documentdb-dotnet-application/image10.png)

3. Nella casella **Nome** digitare il nome del progetto. Questa esercitazione usa il nome "todo". Se si sceglie di usare un valore diverso da questo, quindi ogni volta che in questa esercitazione viene descritto lo spazio dei nomi todo, è necessario modificare gli esempi di codice forniti per usare il nome assegnato all'applicazione. 

4. Fare clic su **Sfoglia** per passare alla cartella in cui si vuole creare il progetto e quindi fare clic su **OK**.

  Verrà visualizzata la finestra di dialogo **Nuovo progetto ASP.NET**.

  ![Schermata della finestra di dialogo Nuovo progetto ASP.NET con il modello MVC evidenziato e la casella Ospita nel cloud selezionata](./media/documentdb-dotnet-application/image11.png)

5. Nel riquadro dei modelli selezionare **MVC**.

6. Se si prevede di ospitare l'applicazione in Azure, selezionare **Ospita nel cloud** in basso a destra per impostare questo comportamento. È stato scelto di ospitare l'applicazione nel cloud e di eseguirla in un sito Web di Azure. Se si seleziona questa opzione, verrà eseguito il pre-provisioning di un sito Web di Azure, in modo da semplificare notevolmente la distribuzione dell'applicazione funzionante finale. Se si intende ospitare l'applicazione altrove o non si vuole configurare Azure in anticipo, deselezionare l'opzione **Ospita nel cloud**.

7. Fare clic su **OK** e lasciare che Visual Studio esegua le operazioni necessarie per lo scaffolding del modello MVC ASP.NET vuoto.

8. Se si è scelto di ospitare l'applicazione nel cloud, verrà visualizzata almeno un'altra schermata che richiede di accedere all'account Azure e di specificare alcuni valori per il nuovo sito Web. Specificare tutti i valori aggiuntivi e continuare.

  Qui non è stato selezionato "Server di database" perché non si intende usare un server di database SQL di Azure, ma verrà creato un nuovo account Azure DocumentDB successivamente nel portale di Microsoft Azure.

	For more information about choosing an **App Service plan** and **Resource group**, see [Azure App Service plans in-depth overview](azure-web-sites-web-hosting-plans-in-depth-overview.md).

  ![Schermata della finestra di dialogo Configura sito Web di Microsoft Azure](./media/documentdb-dotnet-application/image11_1.png)

9. Al termine della creazione dell'applicazione MVC standard da parte di Visual Studio, sarà disponibile un'applicazione ASP.NET vuota eseguibile in locale.

	L'esecuzione locale del progetto verrà ignorata perché è già stata mostrata per l'applicazione ASP.NET "Hello World". Si passerà direttamente all'aggiunta di DocumentDB al progetto e alla compilazione dell'applicazione.

## <a name="_Toc395637767"></a>Passaggio 3: Aggiungere DocumentDB al progetto

Ora che si dispone della maggior parte del plumbing MVC ASP.NET necessario per questa soluzione, è possibile passare all'aggiunta di Azure DocumentDB all'applicazione Web.

1. DocumentDB .NET SDK è distribuito come pacchetto NuGet. Per ottenere il pacchetto NuGet in Visual Studio, usare Gestione pacchetti NuGet in Visual Studio facendo clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e quindi scegliendo **Gestisci pacchetti NuGet**.

  ![Schermata delle opzioni del menu di scelta rapida per il progetto in Esplora soluzioni, con Gestisci pacchetti NuGet evidenziato.](./media/documentdb-dotnet-application/image21.png)

    The **Manage NuGet Packages** dialog box appears.

2. Nella casella **Cerca online** digitare ***Azure DocumentDB***. 
    
    Dai risultati installare il pacchetto delle **librerie client Microsoft Azure DocumentDB**. Il pacchetto DocumentDB verrà scaricato e installato, insieme a tutte le dipendenze, ad esempio Newtonsoft.Json.

  ![Schermata della finestra Gestisci pacchetti NuGet, con la libreria del client di Microsoft Azure DocumentDB evidenziata](./media/documentdb-dotnet-application/nuget.png)

  In alternativa, è possibile usare la console di Gestione pacchetti per installare il pacchetto. A tale scopo, dal menu **Strumenti** fare clic su **Gestione pacchetti NuGet** e quindi su **Console di Gestione pacchetti**. Al prompt dei comandi, immettere quanto segue.

    	Install-Package Microsoft.Azure.DocumentDB

3. Una volta installato il pacchetto, la soluzione di Visual Studio dovrebbe assomigliare alla seguente, con due nuovi riferimenti aggiunti, Microsoft.Azure.Documents.Client e Newtonsoft.Json.

  ![Schermata dei due riferimenti aggiunti al progetto in Esplora soluzioni](./media/documentdb-dotnet-application/image22.png)


##<a name="_Toc395637763"></a>Passaggio 4: Configurare l'applicazione MVC ASP.NET
 
Vengono ora aggiunti i modelli, le visualizzazioni e i controller all'applicazione MVC:

- [Aggiungere un modello](#_Toc395637764).
- [Aggiungere un controller](#_Toc395637765).
- [Aggiungere visualizzazioni](#_Toc395637766).


### <a name="_Toc395637764"></a>Aggiungere un modello

Creare prima di tutto l'elemento che corrisponde alla lettera **M** in MVC, ovvero il modello.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Modelli**, scegliere **Aggiungi** e quindi fare clic su **Classe**.

  Verrà visualizzata la finestra di dialogo **Aggiungi nuovo elemento**.

2. Denominare la nuova classe **Item.cs** e fare clic su **Aggiungi**. 

3. In questo nuovo file **Item.cs**, aggiungere quanto segue dopo l'ultima *istruzione using*.
		
		using Newtonsoft.Json;
	
4. A questo punto sostituire il codice
		
		public class Item
		{
		}

	con il codice seguente:
		
        public class Item
        {
        	[JsonProperty(PropertyName="id")]
        	public string Id { get; set; }
		
        	[JsonProperty(PropertyName="name")]
        	public string Name { get; set; }
		
        	[JsonProperty(PropertyName = "desc")]
        	public string Description { get; set; }
		
       [JsonProperty\(PropertyName="isComplete"\)] public bool Completed { get; set; } }

	Tutti i dati presenti in DocumentDB vengono passati in rete e archiviati come JSON. Per controllare il modo in cui gli oggetti vengono serializzati/deserializzati da JSON.NET, è possibile usare l'attributo **JsonProperty**, come mostrato nella classe **Item** appena creata. Questa operazione **non è necessaria**, ma è utile per verificare che le proprietà seguano le convenzioni di denominazione maiuscole-minuscole JSON.
	
	Non solo è possibile controllare il formato del nome della proprietà quando viene usata in JSON, ma anche rinominare completamente le proprietà .NET, come è stato fatto con la proprietà **Description**.
	

### <a name="_Toc395637765"></a>Aggiungere un controller

Una volta creato il modello **M**, si passa alla creazione di una classe controller **C** in MVC.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Controller**, scegliere **Aggiungi** e quindi fare clic su **Controller**.

    Verrà visualizzata la finestra di dialogo **Aggiungi scaffolding**.

2. Selezionare **Controller MVC 5 - Vuoto** e quindi fare clic su **Aggiungi**.

	![Schermata della finestra di dialogo Aggiungi scaffolding con l'opzione Controller MVC 5 - Vuoto evidenziata](./media/documentdb-dotnet-application/image14.png)

3. Assegnare al controller il nome **ItemController.**

	![Schermata della finestra di dialogo Aggiungi controller](./media/documentdb-dotnet-application/image15.png)

	Una volta creato il file, la soluzione di Visual Studio deve essere simile alla seguente, con il nuovo file ItemController.cs in **Esplora soluzioni**. Viene anche visualizzato il nuovo file Item.cs creato in precedenza.

	![Schermata di Esplora soluzioni con il nuovo file ItemController.cs e il file Item.cs evidenziati](./media/documentdb-dotnet-application/image16.png)

	È possibile chiudere il file ItemController.cs. Si tornerà a questo file in un secondo momento.

### <a name="_Toc395637766"></a>Aggiungere visualizzazioni

A questo punto è necessario creare le visualizzazioni **V** in MVC:

- [Aggiungere una visualizzazione Indice elemento](#AddItemIndexView).
- [Aggiungere una visualizzazione Nuovo elemento](#AddNewIndexView).
- [Aggiungere una visualizzazione Modifica elemento](#_Toc395888515).


#### <a name="AddItemIndexView"></a>Aggiungere una visualizzazione Indice elemento

1. In **Esplora soluzioni** espandere la cartella **Visualizzazioni**, fare clic con il pulsante destro del mouse sulla cartella **Item** vuota creata prima da Visual Studio quando è stato aggiunto **ItemController**, scegliere **Aggiungi** e quindi fare clic su **Visualizzazione**.

	![Schermata di Esplora soluzioni con la cartella Item con i comandi Aggiungi visualizzazione evidenziati](./media/documentdb-dotnet-application/image17.png)

2. Nella finestra di dialogo **Aggiungi visualizzazione** eseguire le operazioni seguenti:
	- Nella casella **Nome visualizzazione** digitare ***Index***.
	- Nella casella **Modello** selezionare ***Elenco***.
	- Nella casella **Classe modello** selezionare ***Item \(todo.Models\)***.
	- Lasciare vuota la casella **Classe contesto dati**. 
	- Nella casella della pagina di layout digitare ***\~/Views/Shared/\_Layout.cshtml***.
	
	![Schermata con la finestra di dialogo Aggiungi visualizzazione](./media/documentdb-dotnet-application/image18.png)

3. Una volta impostati tutti questi valori, fare clic su **Aggiungi**, in modo che venga creata una nuova visualizzazione modello in Visual Studio. Al termine, verrà aperto il file con estensione cshtml creato. È ora possibile chiudere questo file in Visual Studio, perché vi si tornerà di nuovo più avanti.

#### <a name="AddNewIndexView"></a>Aggiungere una visualizzazione Nuovo elemento

Viene ora creata una nuova visualizzazione per la creazione di nuovi oggetti **Item** in modo simile a come è stata creata una visualizzazione **Indice elemento**.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Elemento**, scegliere **Aggiungi** e quindi fare clic su **Visualizzazione**.

2. Nella finestra di dialogo **Aggiungi visualizzazione** eseguire le operazioni seguenti:
    - Nella casella **Nome visualizzazione** digitare ***Create***.
    - Nella casella **Modello** selezionare ***Create***.
    - Nella casella **Classe modello** selezionare ***Item \(todo.Models\)***.
    - Lasciare vuota la casella **Classe contesto dati**.
    - Nella casella della pagina di layout digitare ***\~/Views/Shared/\_Layout.cshtml***.
    - Fare clic su **Aggiungi**.

#### <a name="_Toc395888515"></a>Aggiungere una visualizzazione Modifica elemento

È infine possibile aggiungere un'ultima visualizzazione per la modifica di un oggetto **Item**, allo stesso modo in cui sono state create le altre.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella **Elemento**, scegliere **Aggiungi** e quindi fare clic su **Visualizzazione**.

2. Nella finestra di dialogo **Aggiungi visualizzazione** eseguire le operazioni seguenti:
    - Nella casella **Nome visualizzazione** digitare ***Edit***.
    - Nella casella **Modello** selezionare ***Edit***.
    - Nella casella **Classe modello** selezionare ***Item \(todo.Models\)***.
    - Lasciare vuota la casella **Classe contesto dati**. 
    - Nella casella della pagina di layout digitare ***\~/Views/Shared/\_Layout.cshtml***.
    - Fare clic su **Aggiungi**.

Al termine, chiudere tutti i documenti con estensione cshtml in Visual Studio. Si tornerà a queste visualizzazioni in un secondo momento.

## <a name="_Toc395637769"></a>Passaggio 5: Completamento dell'aggiunta di DocumentDB

Ora che le caratteristiche standard di MVC vengono prese in considerazione, è possibile aggiungere il codice per DocumentDB.

In questa sezione viene aggiunto il codice per gestire le operazioni seguenti:

- [Creare un elenco di elementi incompleti](#_Toc395637770).
- [Aggiungere elementi](#_Toc395637771).
- [Modificare elementi](#_Toc395637772).

### <a name="_Toc395637770"></a>Creare un elenco di elementi incompleti

La prima cosa da fare è aggiungere una classe che contenga tutta la logica per connettersi e usare DocumentDB. Per questa esercitazione verrà inclusa tutta questa logica in una classe di tipo archivio denominata DocumentDBRepository.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Aggiungi** e quindi fare clic su **Classe**. Assegnare alla nuova classe il nome **DocumentDBRepository** e fare clic su **Aggiungi**.
 
2. Appena creata la classe **DocumentDBRepository** e aggiungere le seguenti *istruzioni using* sopra la dichiarazione dello *spazio dei nomi*
		
		using Microsoft.Azure.Documents; 
	    using Microsoft.Azure.Documents.Client; 
	    using Microsoft.Azure.Documents.Linq; 
		using System.Configuration;
		using System.Linq.Expressions;
		using System.Threading.Tasks;

	A questo punto sostituire il codice

		public class DocumentDBRepository
		{
    	}

	con il codice seguente:

    	
		public static class DocumentDBRepository<T>
    	{
			//Use the Database if it exists, if not create a new Database
	    	private static Database ReadOrCreateDatabase()
	    	{
	        	var db = Client.CreateDatabaseQuery()
	        	                .Where(d => d.Id == DatabaseId)
	        	                .AsEnumerable()
	        	                .FirstOrDefault();
				
	        	if (db == null)
	        	{
	        	    db = Client.CreateDatabaseAsync(new Database { Id = DatabaseId }).Result;
	        	}
				
	        	return db;
	    	}
			
			//Use the DocumentCollection if it exists, if not create a new Collection
	    	private static DocumentCollection ReadOrCreateCollection(string databaseLink)
	{ var col = Client.CreateDocumentCollectionQuery\(databaseLink\) .Where\(c =\> c.Id == CollectionId\) .AsEnumerable\(\) .FirstOrDefault\(\);
		
	        	if (col == null)
	        	{
					var collectionSpec = new DocumentCollection { Id = CollectionId };
					var requestOptions = new RequestOptions {OfferType = "S1" };
					
	        	    col = Client.CreateDocumentCollectionAsync(databaseLink, collectionSpec, requestOptions).Result;
	        	}
				
	        	return col;
	    	}
			
			//Expose the "database" value from configuration as a property for internal use
    private static string databaseId; private static String DatabaseId { get { if \(string.IsNullOrEmpty\(databaseId\)\) { databaseId = ConfigurationManager.AppSettings["database"]; }
				
					return databaseId;
				}
    }
			
			//Expose the "collection" value from configuration as a property for internal use
    	    private static string collectionId;
    	    private static String CollectionId
    	    {
				get
				{
					if (string.IsNullOrEmpty(collectionId))
					{
						collectionId = ConfigurationManager.AppSettings["collection"];
					}
				
					return collectionId;
				}
    	    }
			
			//Use the ReadOrCreateDatabase function to get a reference to the database.
    	    private static Database database;
    	    private static Database Database
    	    {
				get
				{
					if (database == null)
					{
						database = ReadOrCreateDatabase();
					}
					
					return database;
				}
    	    }
			
			//Use the ReadOrCreateCollection function to get a reference to the collection.
    	    private static DocumentCollection collection;
    	    private static DocumentCollection Collection
    	    {
				get
				{
					if (collection == null)
					{
						collection = ReadOrCreateCollection(Database.SelfLink);
					}
					
					return collection;
				}
    	    }
			
			//This property establishes a new connection to DocumentDB the first time it is used, 
			//and then reuses this instance for the duration of the application avoiding the
			//overhead of instantiating a new instance of DocumentClient with each request
    	    private static DocumentClient client;
    	    private static DocumentClient Client
    	    {
    	        get
    	        {
    	            if (client == null)
    	            {
						string endpoint = ConfigurationManager.AppSettings["endpoint"];
						string authKey = ConfigurationManager.AppSettings["authKey"];
						Uri endpointUri = new Uri(endpoint);
						client = new DocumentClient(endpointUri, authKey);
    	            }
    	            
    	            return client;
    	        }
    	    }
    	}

	> [AZURE.TIP]Quando si crea un nuovo DocumentCollection è possibile fornire un parametro RequestOptions facoltativo di OfferType, che consente di specificare il livello di prestazioni della nuova raccolta. Se questo parametro non viene passato verrà usato il tipo di offerta predefinito. Per altre informazioni sui tipi di offerta DocumentDB vedere [livelli di prestazioni DocumentDB](documentdb-performance-levels.md)

3. Poiché alcuni valori vengono letti dalla configurazione, aprire il file** Web.config** dell'applicazione e aggiungere le righe seguenti dopo la sezione `<AppSettings>`.
	
    	<add key="endpoint" value="enter the URI from the Keys blade of the Azure portal"/>
    	<add key="authKey" value="enter the PRIMARY KEY, or the SECONDARY KEY, from the Keys blade of the Azure portal"/>
    	<add key="database" value="ToDoList"/>
    	<add key="collection" value="Items"/>
	
4. A questo punto, aggiornare i valori per *endpoint* e *authKey* usando il pannello Chiavi del portale di Azure. Usare il valore di **URI** dal pannello Chiavi come valore dell'impostazione dell'endpoint e usare **CHIAVE PRIMARIA** oppure **CHIAVE SECONDARIA** dal pannello Chiavi come valore dell'impostazione authKey.


    That takes care of wiring up the DocumentDB repository, now let's add our application logic.

5. Un'applicazione per un elenco di azioni deve innanzitutto consentire la visualizzazione degli elementi incompleti. Copiare e incollare il seguente frammento di codice in un punto qualsiasi all'interno della classe **DocumentDBRepository**.

	    public static IEnumerable<T> GetItems(Expression<Func<T, bool>> predicate) 
	    {
			return Client.CreateDocumentQuery<T>(Collection.DocumentsLink) 
				.Where(predicate) 
				.AsEnumerable(); 
		} 

6. Aprire il file **ItemController** aggiunto in precedenza e aggiungere le seguenti *istruzioni using* sopra la dichiarazione dello spazio dei nomi.

		using System.Net;
		using System.Threading.Tasks;
		using todo.Models;

	Se il progetto non è denominato "todo", è quindi necessario eseguire l'aggiornamento usando "todo.Models" per inserire il nome del progetto.

	A questo punto sostituire il codice

		//GET: Item
		public ActionResult Index()
		{
			return View();
		}

	con il codice seguente:

    	public ActionResult Index()
    	{
			var items = DocumentDBRepository<Item>.GetItems(d => !d.Completed);
			return View(items);
    	}
	
A questo punto dovrebbe essere possibile compilare la soluzione senza errori.

Se si eseguisse l'applicazione a questo punto, si passerebbe a **HomeController** e alla visualizzazione **Index** di tale controller. Questo è il comportamento predefinito per il progetto di modello MVC scelto inizialmente, ma è possibile cambiarlo. Per modificare questo comportamento, è possibile cambiare il routing nell'applicazione MVC.

Aprire ***App\\\_Start\\RouteConfig.cs***, trovare la riga che inizia con "defaults:" e modificarla in modo che sia analoga a quanto riportato di seguito.

    	defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Questo codice indica a MVC ASP.NET che, se non è stato specificato alcun valore nell'URL per controllare il comportamento di routing, sarà necessario usare **Item** invece di **Home** come controller e **Index** come visualizzazione.

A questo punto se si esegue l'applicazione, verrà eseguita una chiamata in **ItemController** che eseguirà una chiamata alla classe di tipo archivio e userà il metodo GetItems per restituire tutti gli elementi incompleti alla visualizzazione **Views**\\\*\*Item\*\*\\\*\*Index\*\*.

Se compilato ed eseguito ora, il progetto avrà un aspetto simile al seguente.

![Schermata dell'applicazione Web per un elenco di azioni creata in questa esercitazione](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>Aggiungere elementi

Vengono ora inseriti alcuni elementi nel database, in modo che la griglia non sia più vuota.

Sarà ora effettuata l'aggiunta di codice a DocumentDBRepository e ItemController per rendere permanente il record in DocumentDB.

1.  Aggiungere il metodo seguente alla classe **DocumentDBRepository**.

    	public static async Task<Document> CreateItemAsync(T item)
		{ return await Client.CreateDocumentAsync\(Collection.SelfLink, item\); }

	This method simply takes an object passed to it and persists it in DocumentDB.

2. Aprire il file ItemController.cs e aggiungere il seguente frammento di codice all'interno della classe. In tal modo, ASP.NET MVC individua le operazioni da eseguire per l'azione **Create**. In questo caso, eseguire semplicemente il rendering della visualizzazione Create.cshtml associata creata in precedenza.

    	public ActionResult Create()
    	{ 
			return View(); 
		}

	We now need some more code in this controller that will accept the submission from the **Create** view.

2. Aggiungere il blocco di codice successivo alla classe ItemController.cs, che indica a MVC ASP.NET le operazioni da eseguire con un POST per i moduli per questo controller.
	
    	[HttpPost]
    	[ValidateAntiForgeryToken]
		public async Task<ActionResult> Create\([Bind\(Include = "Id,Name,Description,Completed"\)] Item item\) { if \(ModelState.IsValid\) { await DocumentDBRepository<Item>.CreateItemAsync\(item\); return RedirectToAction\("Index"\); } return View\(item\); } This code calls in to the DocumentDBRepository and uses the CreateItemAsync method to persist the new todo item to the database.
 
	**Security Note**: The **ValidateAntiForgeryToken** attribute is used here to help protect this application against cross-site request forgery attacks. There is more to it than just adding this attribute, your views need to work with this anti-forgery token as well. For more on the subject, and examples of how to implement this correctly, please see [Preventing Cross-Site Request Forgery][]. The source code provided on [GitHub][] has the full implementation in place.

	**Security Note**: We also use the **Bind** attribute on the method parameter to help protect against over-posting attacks. For more details please see [Basic CRUD Operations in ASP.NET MVC][].

Questo è tutto il codice necessario per aggiungere nuovi elementi al database.


### <a name="_Toc395637772"></a>Modificare elementi

Vi è un'ultima operazione da eseguire ovvero l'aggiunta della possibilità di modificare oggetti **Item** nel database e di contrassegnarli come completi. Poiché questa visualizzazione per la modifica è già stata aggiunta al progetto, è sufficiente aggiungere il codice al controller e di nuovo alla classe **DocumentDBRepository**.

1. Aggiungere il codice seguente alla classe **DocumentDBRepository**.

    	public static T GetItem(Expression<Func<T, bool>> predicate)
    	{
        	return Client.CreateDocumentQuery<T>(Collection.DocumentsLink)
                    	.Where(predicate)
                    	.AsEnumerable()
                    	.FirstOrDefault();
    	}		

    	public static async Task<Document> UpdateItemAsync(string id, T item)
    	{
        	Document doc = GetDocument(id);	
        	return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    	}

		private static Document GetDocument(string id) 
		{ 
			return Client.CreateDocumentQuery(Collection.DocumentsLink) 
				.Where(d => d.Id == id) 
				.AsEnumerable() 
				.FirstOrDefault(); 
		} 
	
	Il primo di questi metodi, **GetItem** recupera un elemento da DocumentDB, che viene passato a **ItemController** e quindi alla visualizzazione **Edit**.
	
	Il secondo metodo appena aggiunto sostituisce il **documento** in DocumentDB con la versione del **documento** passato da **ItemController**.

2. Aggiungere il codice seguente alla classe **ItemController**.

    	[HttpPost]
		[ValidateAntiForgeryToken] public async Task<ActionResult> Edit\([Bind\(Include = "Id,Name,Description,Completed"\)] Item item\) { if \(ModelState.IsValid\) { await DocumentDBRepository<Item>.UpdateItemAsync\(item.Id, item\); return RedirectToAction\("Index"\); }

		return View\(item\); }
		
		public ActionResult Edit(string id)
		{
		    if (string.IsNullOrEmpty(id))
		    {
		        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
		    }
					 
		    Item item = (Item)DocumentDBRepository<Item>.GetItem(d => d.Id == id);
		 	
		    if (item == null)
		    {
		        return HttpNotFound();
		    }
		 	
		    return View(item);
		}
		
	
	The first method handles the Http GET that happens when the user clicks on the **Edit** link from the **Index** view. This method fetches a [**Document**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) from DocumentDB and passes it to the **Edit** view.

	The **Edit** view will then do an Http POST to the **IndexController**. 
	
	The second method we added handles passing the updated object to DocumentDB to be persisted in the database.

A questo punto, queste sono le procedure necessarie per eseguire l'applicazione, elencare oggetti **Item** incompleti, aggiungere nuovi oggetti **Item** e modificarli****.

## <a name="_Toc395637773"></a>Passaggio 6: Eseguire l'applicazione in locale

Per testare l'applicazione nel computer locale, eseguire le operazioni seguenti:

1. Premere F5 in Visual Studio per compilare l'applicazione in modalità di debug. L'applicazione verrà compilata e verrà avviato un browser con la pagina di griglia vuota osservata sopra:

	![Schermata dell'applicazione Web per un elenco di azioni creata in questa esercitazione](./media/documentdb-dotnet-application/image24.png)

	In caso di errori a questo punto è possibile confrontare il codice con quello dell'esercitazione todo in [GitHub][].

2. Fare clic sul collegamento **Crea nuovo** e specificare i valori dei campi **Nome** e **Descrizione**. Lasciare deselezionata la casella di controllo **Completed**. In caso contrario, il nuovo oggetto **Item** verrà aggiunto con uno stato completato e non sarà visualizzato nell'elenco iniziale.

	![Schermata della visualizzazione Create](./media/documentdb-dotnet-application/image25.png)

3. Fare clic su **Create** per tornare di nuovo alla visualizzazione **Index** e visualizzare **Item** nell'elenco.

	![Schermata della visualizzazione Index](./media/documentdb-dotnet-application/image26.png)

	È possibile aggiungere altri oggetti **Item** all'elenco di azioni.

3. Fare clic su **Edit** accanto a un oggetto **Item** nell'elenco per tornare alla visualizzazione **Edit**, in cui è possibile aggiornare qualsiasi proprietà dell'oggetto, incluso il flag **Completed**. Se si seleziona il flag **Completed** e si fa clic su **Salva**, l'oggetto **Item** viene rimosso dall'elenco degli attività incomplete.

	![Schermata della visualizzazione Index con la casella Completed selezionata](./media/documentdb-dotnet-application/image27.png)

4. Una volta testata l'app, premere CTRL+F5 per arrestarne il debug. È ora possibile distribuire l'app.

##<a name="_Toc395637774"></a>Passaggio 7: Distribuire l'applicazione in Siti Web di Azure

Ora che è completa e funziona correttamente con DocumentDB, è possibile distribuire questa app Web in Siti Web di Azure. Se alla creazione del progetto MVC ASP.NET vuoto si è selezionato **Ospita nel cloud**, Visual Studio semplifica notevolmente le operazioni eseguendo la maggior parte dei passaggi automaticamente.

1. Per pubblicare l'applicazione, è sufficiente fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica**.

    ![Schermata dell'opzione Pubblica in Esplora soluzioni](./media/documentdb-dotnet-application/image28.png)

2. Ogni impostazione dovrebbe già essere configurata in base alle credenziali e in effetti il sito Web è già stato creato in Azure secondo il valore specificato in **URL di destinazione** ed è sufficiente fare clic su **Pubblica**.

    ![Schermata della finestra di dialogo Pubblica sul Web](./media/documentdb-dotnet-application/image29.png)

Dopo alcuni secondi, Visual Studio completerà la pubblicazione dell'applicazione Web e avvierà un browser in cui sarà possibile ammirare il proprio lavoro in esecuzione in Azure.

##<a name="_Toc395637775"></a>Passaggi successivi

Congratulazioni. È stata creata la prima applicazione MVC ASP.NET con Azure DocumentDB e l'applicazione è stata quindi pubblicata in Siti Web di Azure. Il codice sorgente per l'applicazione completa, insieme alle funzionalità di eliminazione e relative ai dettagli non incluse in questa esercitazione, può essere scaricato o clonato da [GitHub][]. Per aggiungere queste funzionalità all'app, recuperare il codice e aggiungerlo all'app.

Per aggiungere altre funzionalità all'applicazione, esaminare le API disponibili nella raccolta [.NET per DocumentDB](http://msdn.microsoft.com/library/azure/dn783362.aspx), in cui è anche possibile aggiungere il proprio contributo su [GitHub][].

##<a id="GetProject"></a>Ottenere la soluzione da GitHub

Per risparmiare tempo, è anche possibile limitarsi a compilare la soluzione completa todo, senza aggiungere personalmente il codice. La soluzione completa è disponibile su GitHub e può essere compilata e distribuita in pochi minuti in base alle istruzioni seguenti.

1. Assicurarsi che il [software richiesto come prerequisito](#_Toc395637760) sia installato, compresi Visual Studio e Azure SDK per .NET versione 2.3 o successive.

2. Clonare il repository azure-documentdb-net usando Git per Windows [\(http://www.git-scm.com/](http://www.git-scm.com/)\) oppure scaricare il file ZIP da [GitHub](https://github.com/Azure/azure-documentdb-net/).

2. Da Visual Studio aprire il file todo.sln dalla directory azure-documentdb-net/tutorials/todo.

3. Per ripristinare i riferimenti a DocumentDB .NET SDK in Visual Studio 2013, fare clic con il pulsante destro del mouse sulla soluzione todo in **Esplora soluzioni** e quindi fare clic sul comando per **abilitare il ripristino del pacchetto NuGet**.

4. Recuperare i valori di **URI** e **CHIAVE PRIMARIA** o **CHIAVE SECONDARIA** dal pannello **Chiavi** dell'account DocumentDB nel [portale di Azure](https://portal.azure.com/).

	
	Se non si dispone di un account, vedere [Creare un account di database](documentdb-create-account.md) per configurarne uno.

	![Schermata del portale di Azure, che mostra un account DocumentDB, con l'hub ACTIVE evidenziato,  il pulsante Chiavi evidenziato nel pannello dell'account DocumentDB e i valori di URI, CHIAVE PRIMARIA e CHIAVE SECONDARIA evidenziati nel pannello Chiavi](../includes/media/documentdb-keys/keys.png)

5. Nel file Web.config aggiornare i valori predefiniti per le chiavi **endpoint** e **authKey**.

    	<add key="endpoint" value="~enter URI for your DocumentDB Account, from Azure classic portal~" /> 
		<add key="authKey" value="~enter either Primary or Secondary key for your DocumentDB Account, from Azure classic portal~" /> 

	- Copiare il valore di **URI** dal pannello Chiavi e incollarlo nel valore della proprietà **endpoint**. 
	- Copiare il valore di **CHIAVE PRIMARIA** o **CHIAVE SECONDARIA** dal pannello **Chiavi** e incollarlo nel valore della proprietà **authKey**.
	


7. È ora possibile [eseguire l'applicazione localmente](#_Toc395637773) e quindi [distribuirla in Siti Web di Azure](#_Toc395637774).


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Installazione guidata piattaforma Web Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
[GitHub]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Preventing Cross-Site Request Forgery]: http://go.microsoft.com/fwlink/?LinkID=517254
[Basic CRUD Operations in ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=52-->
