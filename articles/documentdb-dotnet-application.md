<properties 
	pageTitle="Creare un'applicazione Web con MVC ASP.NET usando DocumentDB | Azure" 
	description="Informazioni sull'uso di DocumentDB per creare un'applicazione Web To Do List. È possibile usare un'applicazione Web ASP.NET MVC ospitata in Azure per archiviare e accedere ai dati." 
	services="documentdb" 
	documentationCenter="" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="ryancraw"/>

<a name="_Toc395809351">Creare un'applicazione Web con MVC ASP.NET usando DocumentDB</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Panoramica</a>
==============================================================================================

<a name="_Toc395637759">Scenario</a>
------------------------------------

Per evidenziare come i clienti possono sfruttare efficientemente Azure DocumentDB per archiviare ed eseguire query su documenti JSON, questo argomento fornisce una procedura dettagliata end-to-end per la creazione di un'applicazione Web di tipo elenco attività usando Azure DocumentDB.

Questa procedura dettagliata mostra come usare il servizio DocumentDB fornito da Azure per archiviare i dati forniti da un'applicazione Web MVC ASP.NET ospitata in Azure e quindi accedervi. Presuppone anche che si siano già usati MVC ASP.NET e Siti Web di Azure.

Si apprenderà come:

1\. Creare ed eseguire il provisioning di un account DocumentDB

2\. Creare un'applicazione MVC ASP.NET

3\. Connettersi e usare Azure DocumentDB dall'applicazione Web

4\. Distribuire l'applicazione Web in Siti Web di Azure

In questa procedura dettagliata verrà compilata una semplice applicazione di gestione delle attività basata sul Web che consente di creare, recuperare e completare le attività. Le attività verranno memorizzate come documenti JSON in Azure DocumentDB.

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

Prima di seguire le istruzioni di questo articolo, verificare che siano disponibili i seguenti elementi:

Git per Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (o [Visual Studio Express][], che è la versione gratuita)

Azure SDK per .NET versione 2.3 o successive, disponibile tramite l'[Installazione guidata piattaforma Web Microsoft][]

Tutte le schermate in questo documento sono state create tramite Visual Studio 2013 con Update 3 applicato e Azure SDK per .NET versione 2.4. Se il sistema in uso è configurato con versioni diverse, è probabile che le schermate e le opzioni non siano interamente corrispondenti. Se si soddisfano i prerequisiti precedenti, la soluzione dovrebbe funzionare comunque.

<a name="_Toc395637761">Creare un account di database di DocumentDB</a>
================================================================

Per eseguire il provisioning di un account di database di DocumentDB in Azure, aprire il portale di gestione di Azure e fare clic nel riquadro Raccolta di Azure nella home page oppure scegliere "+" nell'angolo inferiore sinistro dello schermo.

![Alt text](./media/documentdb-dotnet-application/image2.png)


Verrà aperta la Raccolta di Azure, da cui sarà possibile selezionare uno tra i molti servizi di Azure disponibili. In Raccolta selezionare "Dati, archiviazione e backup" dall'elenco delle categorie.

![Alt text](./media/documentdb-dotnet-application/image3.png)

Scegliere l'opzione relativa ad Azure DocumentDB

![Alt text](./media/documentdb-dotnet-application/image4.png)


Scegliere "Crea" nella parte inferiore dello schermo

![Alt text](./media/documentdb-dotnet-application/image5.png)

Verrà visualizzato il pannello "Nuovo DocumentDB", dove è possibile specificare il nome, l'area geografica, la scalabilità, il gruppo di risorse e altre impostazioni relative al nuovo account.

![Alt text](./media/documentdb-dotnet-application/image6.png)


Dopo aver specificato i valori dell'account, fare clic su "Crea". Il processo di provisioning avvierà la creazione dell'account di database.
Al termine del processo di provisioning verrà visualizzata una notifica nella relativa area del portale e il riquadro nella schermata iniziale (se si è scelto di crearne uno) verrà modificato per riflettere il completamento dell'azione.

![Alt text](./media/documentdb-dotnet-application/image7.png)


Al termine del processo di provisioning, facendo clic sul riquadro DocumentDB dalla schermata iniziale si avvierà il pannello principale dell'account DocumentDB appena creato.

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


Accedere all'URL e alla chiave primaria dell'endpoint facendo clic sul pulsante "Chiavi", copiare tali valori negli Appunti e tenerli a portata di mano perché verranno usati nell'applicazione Web che verrà creata in seguito.

Verrà ora illustrata in modo dettagliato la procedura per creare un'applicazione MVC ASP.NET completamente nuova. La soluzione completa è disponibile per il download [qui] come riferimento.

<a name="_Toc395637762">Creare una nuova applicazione MVC ASP.NET</a>
================================================================

In Visual Studio scegliere Nuovo progetto dal menu File, quindi selezionare l'opzione per la creazione di una nuova applicazione Web MVC ASP.NET.

![Alt text](./media/documentdb-dotnet-application/image10.png)


Selezionare il percorso in cui creare il progetto, specificare come verrà denominato il progetto (nell'esempio verrà usato "todo") e fare clic su OK.

![Alt text](./media/documentdb-dotnet-application/image11.png)


Se si prevede di ospitare l'applicazione in Azure, selezionare la casella nell'angolo in basso a destra "Ospita nel cloud". È stato scelto l'hosting nel cloud e l'applicazione ed è stata eseguita l'applicazione ospitata in un sito Web di Azure. Se si seleziona questa opzione, verrà eseguito preventivamente il provisioning di un sito Web di Azure, in modo da semplificare notevolmente la distribuzione dell'applicazione funzionante finale. Se si preferisce ospitare l'applicazione altrove o non si desidera configurare Azure anticipatamente, deselezionare l'opzione "Ospita nel cloud".

Fare clic su OK e lasciare che Visual Studio esegua le operazioni necessarie relative allo scaffolding del modello MVC ASP.NET vuoto. Se si sceglie di ospitare l'applicazione nel cloud, verrà visualizzata almeno un'altra schermata che richiede di accedere all'account Azure e di fornire alcuni valori per il nuovo sito Web. Specificare i valori di Azure richiesti e continuare. 

Qui non è stata selezionata una voce in "Server di database", perché non verrà usato un database SQL di Azure. In seguito si creerà un nuovo account Azure DocumentDB nel portale di gestione. 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Al termine della creazione dell'applicazione MVC standard da parte di Visual Studio, sarà disponibile un'applicazione ASP.NET vuota eseguibile in locale.

L'esecuzione locale non sarà esaminata, perché è già stata illustrata per l'applicazione "Hello World" ASP.NET. Si passerà direttamente all'aggiunta di DocumentDB al progetto e alla compilazione dell'applicazione.

</h1>
<a name="_Toc395637767">Aggiungere DocumentDB al progetto</a>
=============================================================

Dopo avere eseguito tutti i passaggi di base necessari per questa soluzione MVC ASP.NET, è possibile passare all'aggiunta di Azure DocumentDB all'applicazione Web.

### 

### <a name="_Toc395637764">Installare il pacchetto NuGet</a>

DocumentDB .NET SDK è distribuito come pacchetto NuGet. È possibile usare Gestione pacchetti NuGet in Visual Studio, disponibile facendo clic con il pulsante destro del mouse sul progetto e scegliendo "Gestisci pacchetti NuGet".

![Alt text](./media/documentdb-dotnet-application/image21.png)

Cercare "Azure DocumentDB" online e installare il pacchetto. Il pacchetto DocumentDB verrà scaricato e installato, insieme a tutte le dipendenze, ad esempio Newtonsoft.Json.

**NOTA:** mentre il servizio è ancora in anteprima, il pacchetto NuGet è contrassegnato come "Preliminare", quindi è necessario includere l'opzione "Includi versione preliminare", altrimenti il pacchetto non verrà trovato. 

In alternativa, è possibile installare il pacchetto tramite la console dei comandi del pacchetto digitando quanto segue:

    Install-Package Microsoft.Azure.Documents.Client -Pre

Dopo l'installazione, la soluzione di Visual Studio dovrebbe avere un aspetto analogo al seguente, con l'aggiunta di due nuovi riferimenti.

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">Configurare l'applicazione MVC ASP.NET</a>
==================================================================

### 

### <a name="_Toc395637764">Aggiungere un modello</a>

Creare prima di tutto l'elemento che corrisponde alla lettera **M** in MVC, ovvero il modello. In Esplora soluzioni fare clic con il pulsante destro del mouse sulla cartella *Models*, quindi scegliere **Aggiungi** e infine **Classe**

![Alt text](./media/documentdb-dotnet-application/image12.png)

Assegnare alla nuova classe il nome **Item**, quindi aggiungere il codice seguente nella nuova classe.

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
		
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
		
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
		
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

Tutti i dati in DocumentDB sono passati tramite la rete e archiviati come JSON. Per controllare il modo in cui gli oggetti sono serializzati/deserializzati da JSON.NET, è possibile usare l'attributo JsonProperty, come illustrato nella classe Item appena creata. Non è **necessario** eseguire questa operazione, ma in questo caso si è voluto verificare che venissero rispettate le convenzioni di denominazione maiuscole-minuscole JSON. 

Non è solo possibile controllare il formato del nome della proprietà quando viene usata in JSON, ma si può rinominare completamente le proprietà .NET, come è stato fatto qui con la proprietà Description. 

Si possono anche usare oggetti JsonConverter e controllare completamente come viene gestita la serializzazione.  

Per fare in modo che Visual Studio risolva l'attributo "JsonProperty" usato nell'esempio, è necessario aggiungere l'istruzione using seguente alla sezione using del file di classe:

    using Newtonsoft.Json;

### <a name="_Toc395637765">Aggiungere un controller</a>

Dopo avere creato il modello, si passa alla creazione dell'elemento corrispondente alla lettera **C** in MVC, ovvero una classe di controller.
In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Controllers*, quindi scegliere **Aggiungi** e infine **Controller**

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


Nella finestra di dialogo **Add Scaffold** fare clic su **MVC 5 Controller - Empty.** Fare clic su **Aggiungi**.

![Alt text](./media/documentdb-dotnet-application/image15.png)

Assegnare al controller il nome **ItemController.**

Visual Studio aggiungerà ItemController. Esplora soluzioni dovrebbe avere un aspetto analogo al seguente.

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">Aggiungere visualizzazioni</a>

Si passerà ora alla creazione dell'elemento corrispondente alla lettera **V** in MVC, ovvero una visualizzazione.


#### Aggiungere la visualizzazione di indice per gli elementi

Espandere la cartella ***Views*** in Esplora soluzioni e individuare la cartella Item (vuota) creata da Visual Studio durante l'aggiunta di *ItemController*eseguita in precedenza. Fare clic con il pulsante destro del mouse su ***Item*** e scegliere di aggiungere una nuova visualizzazione.

![Alt text](./media/documentdb-dotnet-application/image17.png)

Nella finestra di dialogo "Aggiungi visualizzazione" assegnare alla visualizzazione il nome ***Index***, usare il modello ***List***, selezionare ***Item (todo.Models)*** creato in precedenza come classe e infine usare ***~Views/Shared/_Layout.cshtml*** nella soluzione come pagina di layout.

![Alt text](./media/documentdb-dotnet-application/image18.png)


Dopo avere configurato questi valori, fare clic su Aggiungi, in modo che Visual Studio crei automaticamente la visualizzazione. Verrà creata una visualizzazione modello in Visual Studio. Al termine, verrà aperto il file con estensione cshtml creato. Chiudere il documento in Visual Studio. Più avanti si tornerà a questo documento.

#### Aggiungere una nuova visualizzazione per gli elementi

In modo analogo alla procedura precedente, creare una nuova visualizzazione per la creazione di nuovi elementi, come illustrato nell'esempio seguente.

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### Aggiungere una visualizzazione di modifica degli elementi

<a name="_Toc395888515"></a>
=======================================

Aggiungere infine una visualizzazione per la modifica di un elemento usando la procedura riportata in precedenza.

![Alt text](./media/documentdb-dotnet-application/image20.png)


Al termine, chiudere i documenti con estensione cshtml in Visual Studio. Si tornerà in seguito a queste visualizzazioni.

</h1>
<a name="_Toc395637769">Completare l'aggiunta di DocumentDB</a>
------------------------------------------------

### <a name="_Toc395637770">Elencare gli elementi incompleti</a>

Aprire **ItemController** e rimuovere tutto il codice nella classe aggiunta da Visual Studio, ma lasciare la classe. La classe verrà ricostruita tramite DocumentDB.

Aggiungere il frammento di codice seguente nella classe ItemController attualmente vuota.

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

Questo codice usa anche una classe di tipo "pseudorepository" denominata DocumentDBRepository, che deve ancora essere creata. Si tratta in effetti di una classe helper che contiene tutto il codice specifico di DocumentDB. Ai fini di questa procedura dettagliata non verrà implementato un livello di accesso completo ai dati con inserimento di dipendenze e con factory e modelli di repository, cosa che sarebbe necessaria per la creazione di un'applicazione per il mondo reale. 
In questa procedura dettagliata tutta la logica di accesso ai dati sarà semplicemente inserita in un progetto, per semplificare le operazioni e concentrarsi sul codice specifico di DocumentDB.

Aggiungere una nuova classe al progetto e denominarla **DocumentDBRepository.**
Sostituire il codice nel file di classe con il seguente.

    public static class DocumentDBRepository
    {
        private static string databaseId;
        private static String DatabaseId
        {
			get
			{
				if (string.IsNullOrEmpty(databaseId))
				{
					databaseId = ConfigurationManager.AppSettings["database"];
				}
			
				return databaseId;
			}
        }
			  
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

#### 

Risolvere tutti gli spazi dei nomi in Visual Studio. Tutti gli spazi dei nomi potranno essere risolti con facilità in Visual Studio, purché il pacchetto NuGet sia stato installato correttamente. I riferimenti ai metodi ReadOrCreateDatabase e ReadOrCreateCollection rimarranno non risolti finché non verranno aggiunti, operazione che verrà eseguita successivamente. 

Per la lettura e la creazione di raccolte documenti e database di DocumentDB, vengono usate due chiamate ai metodi.
Aggiungere quindi alla classe i due metodi seguenti:

    private static DocumentCollection ReadOrCreateCollection(string databaseLink)
    {
        var col = Client.CreateDocumentCollectionQuery(databaseLink)
                          .Where(c => c.Id == CollectionId)
                          .AsEnumerable()
                          .FirstOrDefault();

        if (col == null)
        {
            col = Client.CreateDocumentCollectionAsync(databaseLink, new DocumentCollection { Id = CollectionId }).Result;
        }
		
        return col;
    }

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

</h1>

#### 

Vengono configurati gli elementi Database e DocumentCollection e viene creato il codice per la connessione a DocumentDB tramite DocumentClient. 

Per la lettura di alcuni valori della configurazione, aprire il file **web.config** e aggiungere le righe seguenti nella sezione
<AppSettings\>:

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>


</h4>

Sarà ora effettuata l'aggiunta di codice per svolgere le attività. 
La prima operazione che si deve poter eseguire con un'applicazione elenco attività è visualizzare gli elementi incompleti. Il metodo seguente esegue automaticamente questa operazione, quindi è possibile copiarlo e incollarlo in un punto qualsiasi della classe di tipo archivio che si sta usando.
    
    public static List<Item> GetIncompleteItems()
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => !d.Completed)
                .AsEnumerable()
                .ToList<Item>();
    }
    
Il riferimento a *CreateDocumentQuery* sarà risolto dopo l'aggiunta manuale della direttiva using seguente.

    using Microsoft.Azure.Documents.Linq;

A questo punto dovrebbe essere possibile compilare la soluzione senza errori.

Se si eseguisse ora l'applicazione, si passerebbe al controller Home e alla visualizzazione Index del controller. Questo è il comportamento predefinito per il progetto di modello MVC scelto inizialmente, ma è possibile cambiarlo. Per modificare questo comportamento, è possibile cambiare il routing nell'applicazione MVC.

Aprire***App\_Start\RouteConfig.cs***, trovare la riga che inizia con "defaults:" e modificarla in modo che sia analoga a quanto riportato di seguito.

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Questo codice indica a MVC ASP.NET che, se non è stato specificato alcun valore in URL per controllare il comportamento di routing, sarà necessario usare "Item" invece di "Home" come controller e Index come visualizzazione.
Se si esegue l'applicazione, verrà effettuata una chiamata a **ItemController** e i risultati del metodo **GetIncompleteItems** verranno restituiti alla visualizzazione Views\Item\Index. 

Se lo si compila ed esegue ora, il progetto avrà un aspetto analogo al seguente.    

![Alt text](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771">Aggiunta di elementi</a>

Verranno aggiunti alcuni elementi al database, in modo che non sia disponibile solo una griglia vuota.

Sono già disponibili una visualizzazione per la creazione e un pulsante nella visualizzazione Index, che porterà l'utente alla visualizzazione Create. Sarà ora effettuata l'aggiunta di codice al controller e al repository per rendere permanente il record in DocumentDB.

Aprire il file ***ItemController.cs*** e aggiungere il frammento di codice seguente, in modo che MVC ASP.NET riconosca quali operazioni dovranno essere eseguite per l'azione Create. In questo caso verrà semplicemente eseguito il rendering della visualizzazione Create.cshtml associata creata in precedenza.

    public ActionResult Create()
    { 
	return View(); 
    }

È ora necessario aggiungere altro codice al controller, in modo da accettare gli invii dalla visualizzazione Create.

Aggiungere il blocco di codice successivo, che indica a MVC ASP.NET le operazioni da eseguire con un POST per i moduli per questo controller.
	
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Create([Bind(Include = "Id,Name,Description,Completed")] Item item)  
    {
		if (ModelState.IsValid)  
		{  
		    await repo.CreateDocument(item);  
		    return RedirectToAction("Index");  
		}   
    	return View(item);   
    }

**Nota sulla sicurezza**: l'attributo [ValidateAntiForgeryToken] viene usato qui per proteggere questa applicazione dagli attacchi di tipo richiesta intersito falsa. Oltre ad aggiungere questo attributo, è anche necessario che le visualizzazioni usino questo token antifalsificazione. Per altre informazioni sull'argomento ed esempi di corretta implementazione, vedere l'articolo che descrive [come prevenire le richieste intersito false][]. Nel codice sorgente, disponibile facendo clic sul collegamento per il download alla fine dell'articolo, è definita l'implementazione completa.

**Nota sulla sicurezza**: viene usato anche l'attributo [Bind] nel parametro del metodo per migliorare la protezione contro gli attacchi basati su un eccesso di post. Per informazioni dettagliate, vedere l'articolo relativo alle [operazioni CRUD di base in MVC ASP.NET][]

Dopo questa operazione, il controller Items passerà l'elemento, in modo sicuro, dal modulo al metodo CreateDocument della classe di tipo repository. Aggiungere quindi il metodo seguente alla classe DocumentDBRepository.

    public static async Task<Document> CreateItemAsync(Item item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item);
    }

Questo metodo accetta semplicemente un oggetto passato al metodo stesso e lo rende permanente in DocumentDB.
Questo è il codice necessario per aggiungere nuovi elementi al database.


### <a name="_Toc395637772">Modifica degli elementi</a>

È ora necessario aggiungere la possibilità di modificare gli elementi nel database e di contrassegnarli come completati al termine delle attività. Analogamente alla visualizzazione per l'aggiunta, la visualizzazione per la modifica è già disponibile, quindi è sufficiente aggiungere altro codice al controller e alla classe DocumentDBRepository.

Aggiungere il codice seguente alla classe ItemController.

    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    {
        if (ModelState.IsValid)
        {
            await DocumentDBRepository.UpdateItemAsync(item);
            return RedirectToAction("Index");
        }

        return View(item);
    }
	
    public ActionResult Edit(string id)
    {
        if (id == null)
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
        }

        Item item = (Item)DocumentDBRepository.GetItem(id);
        if (item == null)
        {
            return HttpNotFound();
        }

        return View(item);
    }

Il primo metodo gestisce l'operazione Http Get che si verificherà quando l'utente farà clic sul collegamento "Edit" nella visualizzazione Index. Questo metodo recupera un documento da DocumentDB e lo passa alla visualizzazione di modifica.

La visualizzazione di modifica eseguirà quindi un'operazione Http Post in IndexController. 
Il secondo metodo aggiunto gestirà il passaggio dell'oggetto aggiornato a DocumentDB, in modo che sia reso permanente nel database.

Aggiungere il codice seguente alla classe DocumentDBRepository.

    public static Item GetItem(string id)
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => d.Id == id)
                    .AsEnumerable()
                    .FirstOrDefault();
    }
	
    public static async Task<Document> UpdateItemAsync(Item item)
    {
        Document doc = Client.CreateDocumentQuery(Collection.DocumentsLink)
                            .Where(d => d.Id == item.Id)
                            .AsEnumerable()
                            .FirstOrDefault();

        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

Il primo dei due metodi recupera un elemento da DocumentDB, che viene passato a ItemController e quindi alla visualizzazione di modifica.

Il secondo metodo appena aggiunto sostituisce il documento in DocumentDB con la versione del documento passata da ItemController.

Queste sono le procedure necessarie per eseguire l'applicazione, elencare gli elementi incompleti, aggiungere nuovi elementi e infine modificare gli elementi.

</h3>
<a name="_Toc395637773">Eseguire l'applicazione in locale</a>
=========================================================

### 

Per eseguire il test dell'applicazione nel computer locale, premere F5 in Visual Studio. L'app verrà compilata e verrà avviato un browser con la pagina di griglia vuota riportata in precedenza:

![Alt text](./media/documentdb-dotnet-application/image24.png)

1\.Usare i campi forniti per Item, Item Name e Category per l'immissione delle informazioni, quindi fare clic sul collegamento **"Create New"** e specificare alcuni valori. Lasciare deselezionata la casella di controllo "Completed". In caso contrario, il nuovo elemento verrà aggiunto con uno stato completato e non sarà visualizzato nell'elenco iniziale.

![Alt text](./media/documentdb-dotnet-application/image25.png)

Se si fa clic su Create, si verrà reindirizzati alla pagina Index e l'elemento dovrebbe essere visualizzato nell'elenco.

![Alt text](./media/documentdb-dotnet-application/image26.png)

È possibile aggiungere altri elementi all'elenco attività.

2\.Fare clic su "Edit" accanto a un elemento nell'elenco. Verrà aperta la visualizzazione di modifica, in cui è possibile aggiornare qualsiasi proprietà dell'oggetto, incluso il flag "Completed". L'elemento sarà quindi contrassegnato come completato e verrà rimosso dall'elenco di attività incomplete.

![Alt text](./media/documentdb-dotnet-application/image27.png)

3\.Per completare un'attività, è sufficiente selezionare la casella di controllo, quindi fare clic su **Save.** Si sarà reindirizzati alla pagina di elenco e l'elemento non sarà più incluso nell'elenco.

</h3>
<a name="_Toc395637774">Distribuire l'applicazione in Siti Web di Azure</a>
================================================================

### 

Ora che l'applicazione completa funziona correttamente con DocumentDB, sarà possibile distribuirla in Siti Web di Azure.

Se è stata selezionata l'opzione "Ospita nel cloud" durante la creazione del progetto MVC ASP.NET vuoto, Visual Studio semplificherà notevolmente le operazioni, eseguendo la maggior parte delle procedure automaticamente. Per pubblicare l'applicazione è sufficiente fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni (assicurarsi che non sia ancora in esecuzione localmente), quindi scegliere Pubblica.

![Alt text](./media/documentdb-dotnet-application/image28.png)

Dovrebbe essere tutto già configurato in base alle credenziali specificate. Il sito Web è già stato in effetti creato automaticamente in Azure all'indirizzo specificato in "URL di destinazione". È quindi sufficiente fare clic su **Pubblica**

![Alt text](./media/documentdb-dotnet-application/image29.png)

Dopo alcuni secondi, Visual Studio completerà la pubblicazione dell'applicazione Web e avvierà un browser in cui sarà possibile ammirare il proprio lavoro in esecuzione in Azure.

</h3>



<a name="_Toc395637775">Conclusioni</a>
======================================

### 

Congratulazioni. Si è creata la prima applicazione MVC ASP.NET usando Azure DocumentDB e la si è pubblicata in Siti Web di Azure. Il codice sorgente per l'applicazione completa, inclusa la funzionalità Details & Delete che qui è stata ignorata, è disponibile per il download [qui][].


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/it-it/products/visual-studio-express-vs.aspx
[Installazione guidata piattaforma Web Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
[qui]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Prevenzione delle richieste intersito false]: http://go.microsoft.com/fwlink/?LinkID=517254
[Operazioni CRUD di base in MVC ASP.NET]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=27-->

<!--HONumber=46--> 
