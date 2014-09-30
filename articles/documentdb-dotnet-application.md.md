<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Build a web application with ASP.NET MVC using DocumentDB | Azure" description="Learn how to use DocumentDB to build a To Do List web application. You'll store and access data from an ASP.NET MVC web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="hawong"></tags>

# <a name="_Toc395809351">Creare un'applicazione Web con MVC ASP.NET usando DocumentDB</a>

# <a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Panoramica</a>

## <a name="_Toc395637759">Scenario</a>

Per evidenziare come i clienti possono sfruttare efficientemente Azure DocumentDB per
archiviare ed eseguire query su documenti JSON, questo argomento fornisce una procedura dettagliata end-to-end
per la creazione di un'applicazione Web di tipo elenco attività usando Azure Document
DB.

Questa procedura dettagliata mostra come usare il servizio DocumentDB fornito da
Azure per archiviare e accedere ai dati forniti da un'applicazione Web MVC ASP.NET
ospitata in Azure e presuppone che si siano già usati
MVC ASP.NET e Siti Web di Azure.

Si apprenderà come:

1. Creare ed eseguire il provisioning di un account DocumentDB

2. Creare un'applicazione MVC ASP.NET

3. Connettersi e usare Azure DocumentDB dall'applicazione Web

4. Distribuire l'applicazione Web in Siti Web di Azure

In questa procedura dettagliata verrà creata una semplice
applicazione di gestione delle attività basata su Web che permette di creare, recuperare e
completare le attività. Le attività verranno archiviate come documenti JSON in Azure
DocumentDB.

![Alt text][]

# <a name="_Toc395637760">Prerequisiti</a>

Prima di seguire le istruzioni di questo articolo, verificare
che siano installati i seguenti elementi:

Git per Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (o [Visual Studio Express][], che è la
versione gratuita)

Azure SDK per .NET versione 2.3 o successive, disponibile tramite l'
[Installazione guidata piattaforma Web Microsoft][]

Tutte le schermate in questo documento sono state create tramite
Visual Studio 2013 con Update 3 applicato e Azure SDK per .NET versione
2.4. Se il sistema è configurato con versioni diverse, è probabile che le
schermate e le opzioni non siano interamente corrispondenti. Se si soddisfano i
prerequisiti precedenti, la soluzione dovrebbe funzionare comunque.

# <a name="_Toc395637761">Creare un account di database di DocumentDB</a>

Per eseguire il provisioning di un account di database di DocumentDB in Azure, aprire il
portale di gestione di Azure e fare clic nel riquadro Raccolta di Azure nella
home page oppure scegliere "+" nell'angolo inferiore sinistro dello schermo.

![Alt text][1]

Verrà aperta la Raccolta di Azure, da cui sarà possibile selezionare uno tra i molti
servizi di Azure disponibili. Nella raccolta scegliere "Dati, archiviazione, cache e
backup" dall'elenco delle categorie.

![Alt text][2]

Scegliere l'opzione relativa ad Azure DocumentDB

![Alt text][3]

Scegliere "Crea" nella parte inferiore dello schermo

![Alt text][4]

Verrà aperto il pannello "Nuovo DocumentDB" in cui sarà possibile specificare
nome, area, scala, gruppo di risorse e altre impostazioni per il nuovo
account.

![Alt text][5]

Dopo aver fornito i valori per l' account, fare clic su "Crea" e il processo di provisioning avvierà la creazione dell'account.
Al termine del processo verrà visualizzata una notifica
nell'area di notifica del portale e il riquadro nella schermata
iniziale (se si è scelto di crearne una) verrà modificato per riflettere
il completamento dell'azione.

![Alt text][6]

Dopo il completamento del provisioning, facendo clic sul pannello DocumentDB della schermata
iniziale si avvierà il pannello principale per l'account
DocumentDB appena creato.

![Alt text][7]
![Alt text][8]

Usando il pulsante "Chiavi" accedere all'URL e alla chiave primaria del proprio endpoint,
copiare tali valori negli Appunti e mantenerli a portata di mano poiché verranno usati
nell'applicazione Web che verrà creata in seguito.

Verrà ora illustrata in modo dettagliato la procedura per creare un'applicazione MVC ASP.NET
completamente nuova. La soluzione completa è disponibile per il
download [qui] come riferimento.

# <a name="_Toc395637762">Creare una nuova applicazione MVC ASP.NET</a>

In Visual Studio scegliere Nuovo progetto dal menu File, quindi selezionare l'opzione per la
creazione di una nuova applicazione Web MVC ASP.NET.

![Alt text][9]

Selezionare il percorso in cui creare il progetto, quindi fare clic su OK.

![Alt text][10]

Se si prevede di ospitare l'applicazione in Azure, selezionare la casella nell'angolo in basso a destra "Ospita nel cloud". È stato scelto l'hosting nel cloud e l'applicazione ed è stata eseguita l'applicazione ospitata in un sito Web di Azure. Se si seleziona
questa opzione, verrà eseguito preventivamente il provisioning di un sito Web di Azure, in modo da semplificare notevolmente
la distribuzione dell'applicazione funzionante finale.

Fare clic su OK e lasciare che Visual Studio esegua le operazioni necessarie relative allo scaffolding del
modello MVC ASP.NET vuoto. È possibile che siano visualizzate schermate aggiuntive che richiedono di
accedere all'account Azure e fornire alcuni valori per il nuovo
sito Web. Specificare i valori di Azure richiesti e continuare.

Al termine della creazione dell'applicazione MVC standard da parte di Visual Studio,
sarà disponibile un'applicazione ASP.NET vuota eseguibile in locale.

L'esecuzione locale non sarà esaminata, perché è già stata illustrata per l'applicazione "Hello World" ASP.NET. Si passerà direttamente all'aggiunta di DocumentDB al progetto e alla compilazione dell'applicazione.

# <a name="_Toc395637763">Configurare l'applicazione MVC ASP.NET</a>

### 

### <a name="_Toc395637764">Aggiungere un modello</a>

Creare prima di tutto l'elemento che corrisponde alla lettera **M** in MVC, ovvero il modello. In Esplora
soluzioni fare clic con il pulsante destro del mouse sulla cartella *Modelli*, quindi scegliere **Aggiungi** e infine
**Classe**

![Alt text][11]

Assegnare alla nuova classe il nome **Item**, quindi aggiungere il codice seguente nella
nuova classe.

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public int ID { get; set; }
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

Tutti i dati in DocumentDB sono passati tramite la rete e archiviati come JSON. Per
controllare il modo in cui gli oggetti sono serializzati/deserializzati da JSON.NET, è possibile
usare l'attributo JsonProperty riportato in precedenza, che assicura che i
nomi di proprietà siano conformi alla convenzione JSON relativa ai nomi di proprietà in minuscolo.
Non è **necessario** eseguire questo passaggio.

### <a name="_Toc395637765">Aggiungere un controller</a>

Dopo avere creato il modello, si passa alla creazione dell'elemento corrispondente alla lettera **C** in MVC, ovvero una
classe di controller.
 In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella *Controllers*, quindi
scegliere **Aggiungi** e infine **Controller**.

![Alt text][12]

![Alt text][13]

Nella finestra di dialogo **Add Scaffold** fare clic su **MVC 5 Controller - Empty.**
Fare clic su **Aggiungi.**

![Alt text][14]

Assegnare al controller il nome **ItemController.**

Visual Studio aggiungerà ItemController. Esplora
soluzioni dovrebbe avere un aspetto analogo al seguente.

![Alt text][15]

### <a name="_Toc395637766">Aggiungere visualizzazioni</a>

Si passerà ora alla creazione dell'elemento corrispondente alla lettera **V** in MVC, ovvero una visualizzazione.

#### Aggiungere la visualizzazione di indice per gli elementi

Espandere la cartella ***Views*** in Esplora soluzioni e individuare la cartella
Item (vuota) creata da Visual Studio durante l'
aggiunta di *ItemController* eseguita in precedenza. Fare clic con il pulsante destro del mouse su ***Item***
e scegliere di aggiungere una nuova visualizzazione.

![Alt text][16]

Nella finestra di dialogo "Aggiungi visualizzazione" assegnare alla visualizzazione il nome "***Index***", usare il modello
***List***, selezionare ***Item (Todo.Models)***, creato
in precedenza come classe e infine usare ***\_Layout.cshtml***
nella soluzione come pagina di layout.

![Alt text][17]

Dopo avere configurato questi valori, fare clic su Aggiungi, in modo che Visual Studio crei
automaticamente la visualizzazione. Verrà creata una visualizzazione modello in Visual Studio. Al termine
, verrà aperto il file con estensione cshtml creato. Chiudere il documento
in Visual Studio. Più avanti si tornerà a questo documento.

#### Aggiungere una nuova visualizzazione per gli elementi

In modo analogo alla procedura precedente, creare una nuova visualizzazione per la creazione di nuovi elementi,
come illustrato nell'esempio seguente.

![Alt text][18]

#### Aggiungere una visualizzazione di modifica degli elementi

# <a name="_Toc395888515"></a>

Aggiungere infine una visualizzazione per la modifica di un elemento usando la procedura riportata
in precedenza.

![Alt text][19]

Al termine, chiudere i documenti con estensione Visual Studio. Si
tornerà in seguito a queste visualizzazioni.

</h1>
# <a name="_Toc395637767">Aggiungere DocumentDB al progetto</a>

Dopo avere eseguito tutti i passaggi di base necessari per questa soluzione
MVC ASP.NET, è possibile passare all'aggiunta di Azure DocumentDB all'applicazione Web.

</h1>
## <a name="_Toc395637768">Installazione del pacchetto NuGet per DocumentDB</a>

DocumentDB .NET SDK è distribuito come pacchetto NuGet.
È possibile usare la gestione pacchetti NuGet in Visual Studio, disponibile facendo
clic con il pulsante destro del mouse sul progetto e scegliendo "Gestisci pacchetti NuGet".

![Alt text][20]

Cercare "Azure DocumentDB" online e installare il pacchetto. Il
pacchetto DocumentDB verrà scaricato e installato, insieme a tutte le
dipendenze, ad esempio Newtonsoft.Json.

Dopo l'installazione, la soluzione di Visual Studio dovrebbe avere un aspetto analogo al seguente,
con l'aggiunta di due nuovi riferimenti.

![Alt text][21]

</h1>
## <a name="_Toc395637769">Completamento dell'aggiunta di DocumentDB</a>

### <a name="_Toc395637770">Elencare gli elementi incompleti</a>

Aprire **ItemController** e rimuovere tutto il codice nella classe
(ma lasciare la classe) aggiunta da Visual Studio. La classe verrà ricostruita tramite DocumentDB.

Aggiungere il frammento di codice seguente nella classe ItemController attualmente
vuota.

    public async Task<ActionResult> Index() 
    {
        var items = await repo.GetIncompleteItems();
        return View(items);
    }

Verrà usata una classe di tipo "pseudo-archivio" per DocumentDB. Si tratta in effetti
semplicemente di una classe Helper contenente tutto il codice specifico per DocumentDB. Ai
fini di questa procedura dettagliata, non verrà implementato un livello di
accesso completo ai dati con inserimento di dipendenze tramite un modello di archivio,
cosa che sarebbe necessaria per la creazione di un'applicazione per il mondo reale. In questa
procedura dettagliata tutta la logica di accesso ai dati
sarà semplicemente inserita in un progetto, per semplificare le operazioni.

Aggiungere una nuova classe al progetto e assegnare alla classe il nome **DocumentDBRepository.**
Sostituire quindi il codice nel file della classe con il codice seguente.

    public static class DocumentDBRepository
    {
        public static async Task<List<Item>> GetIncompleteItems()
        {
            return await Task<List<Item>>.Run( () => 
                Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => !d.Completed)
                    .AsEnumerable()
                    .ToList<Item>());
        }
    } 

#### 

Aggiungere le istruzioni using appropriate per risolvere i
riferimenti. Tutti i riferimenti potranno essere risolti con facilità in Visual Studio,
purché il pacchetto NuGet sia stato installato correttamente. Il riferimento a
*CreateDocumentQuery* sarà risolto dopo l'aggiunta manuale della
direttiva using seguente.

    using Microsoft.Azure.Documents.Linq; 

Per ottenere il risultato corretto, è necessario eseguire ancora alcune operazioni
di base sulla classe di tipo pseudo-archivio. Aggiungere il codice
seguente alla classe di tipo archivio.


        private static Database database;
        private static Database Database
        {
            get
            {
                if (database == null)
                {
                    ReadOrCreateDatabase().Wait();
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
                    ReadOrCreateCollection(Database.SelfLink).Wait();
                }
                
                return collection;
            }
        }

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

        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
                    String endpoint = ConfigurationManager.GetSetting("endpoint")
                    string authKey = ConfigurationManager.GetSetting("authKey");
                    Uri endpointUri = new Uri(endpoint);
                    client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }

        private static async Task ReadOrCreateCollection(string databaseLink)
        {
            var collections = Client.CreateDocumentCollectionQuery(databaseLink)
                              .Where(col => col.Id == CollectionId).ToArray();
            
            if (collections.Any())
            {
                collection = collections.First();
            }
            else
            {
                collection = await Client.CreateDocumentCollectionAsync(databaseLink, 
                    new DocumentCollection { Id = CollectionId });
            }
        }
     
        private static async Task ReadOrCreateDatabase()
        {
            var databases = Client.CreateDatabaseQuery()
                            .Where(db => db.Id == DatabaseId).ToArray();
            
            if (databases.Any())
            {
                database = databases.First();
            }
            else
            {
                Database database = new Database { Id = DatabaseId };   
                database = await Client.CreateDatabaseAsync(database);
            }
        }

</h1>
#### 

Quando si accede per la prima volta a un metodo quale GetIncompleteItems(), sarà effettuata una chiamata a
Client, che stabilisce una connessione ad Azure DocumentDB. La prima
chiamata a GetIncompleteItems() tenterà anche di leggere o creare gli elementi
Database e DocumentCollection per questa applicazione. Questa operazione deve essere
eseguita solo una volta. In seguito sarà sufficiente memorizzare nella cache le proprietà SelfLink per
entrambe le risorse e riutilizzarle nell'applicazione.

Se in seguito ci si vuole connettere a DocumentDB nella stessa applicazione
, sarà riutilizzata la stessa istanza di Client, Database e Collection,
invece di creare ogni volta una nuova istanza.

Aprire il file **web.config** e aggiungere le righe seguenti nella sezione
<appsettings\>.

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>

</h4>
A questo punto dovrebbe essere possibile compilare la soluzione senza errori.

Se si eseguisse ora l'applicazione, si passerebbe a Home Controller e
alla visualizzazione Index del controller. Questo è il comportamento predefinito per il
progetto di modello MVC scelto inizialmente, ma è possibile cambiarlo. Per
modificare questo comportamento, è possibile cambiare il routing nell'applicazione MVC.

Aprire il file ***RouteConfig.cs*** nella cartella App\_Start, individuare la riga
che inizia con "defaults:" e modificarla in modo che sia analoga a quanto riportato di seguito.

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Questo codice indica a MVC ASP.NET che se non è stato specificato alcun valore in URL per controllare il comportamento di routing, sarà necessario usare "Item" invece di "Home" come controller e Index come visualizzazione.
Se si esegue l'applicazione, verrà effettuata una chiamata per **ItemController** e i risultati del metodo **GetIncompleteItems** verranno restituiti alla visualizzazione Views\\Item\\Index.
Se lo si esegue ora, il progetto avrà un aspetto analogo al seguente.

![Alt text][22]

### <a name="_Toc395637771">Aggiunta di elementi</a>

Verranno aggiunti alcuni elementi al database, in modo che non sia
disponibile solo una griglia vuota.

Sono già disponibili una visualizzazione per la creazione e un pulsante nella visualizzazione Index
, che porterà l'utente alla visualizzazione Create. Sarà ora effettuata l'aggiunta di codice al
controller e all'archivio per rendere permanente il record in DocumentDB.

Aprire il file ***ItemController.cs*** e aggiungere il frammento di codice seguente,
in modo che MVC ASP.NET sappia quali operazioni eseguire per l'azione Create. In questo caso
verrà semplicemente eseguito il rendering della visualizzazione Create.cshtml associata creata in precedenza.

    public ActionResult Create()
    { 
          return View(); 
    }

È ora necessario aggiungere altro codice al controller, in modo da accettare gli
invii dalla visualizzazione Create.

Aggiungere quindi il blocco di codice successivo, che indica a MVC ASP.NET le operazioni da
eseguire con un POST per i moduli per questo controller.

    [HttpPost]  
    public async Task<ActionResult> Create(Item item)  
    {
        if (ModelState.IsValid)  
        {  
            await repo.CreateDocument(item);  
            return RedirectToAction("Index");  
        }   
        return View(item);   
    }

Il controller Items passerà ora l'elemento dal modulo al metodo
CreateDocument della classe di tipo pseudo-archivio. Aggiungere quindi il
metodo seguente alla classe DocumentDBRepository.

    public static async Task<Document> CreateDocument(dynamic item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item); 
    }

Questo metodo accetta semplicemente qualsiasi oggetto passato al metodo stesso e lo rende permanente in
DocumentDB.

Questo è il codice necessario per aggiungere nuovi elementi al database.

### <a name="_Toc395637772">Modifica degli elementi</a>

È ora necessario aggiungere la possibilità di modificare
gli elementi nel database e di contrassegnarli come completati al termine delle
attività. Analogamente alla visualizzazione per l'aggiunta, la visualizzazione per la modifica è già disponibile, quindi è
sufficiente aggiungere altro codice al controller e alla classe
DocumentDBRepository.

Aggiungere il codice seguente alla classe ItemController.

    public async Task<ActionResult> Edit(string id)    
    {  
        if (id == null)  
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);   
        }

        Item item = await repo.GetDocument(id);
        if (item == null)
        {       
            return HttpNotFound();
        }
        
        return View(item);
    }

    [HttpPost] 
    public async Task<ActionResult> Edit(Item item)  
    {
        if (ModelState.IsValid)
        {
             await repo.UpdateDocument(item);
             return RedirectToAction("Index");
        }
        
        return View(item); 
    }

Il primo metodo gestisce l'operazione Http Get che si verificherà quando l'utente
fa clic sul collegamento "Edit" nella visualizzazione Index. Questo metodo recupera un
documento da DocumentDB e lo passa alla visualizzazione Edit.

### 

La visualizzazione Edit eseguirà quindi un'operazione Http Post in IndexController e
il secondo metodo aggiunto gestirà il passaggio dell'oggetto aggiornato
a DocumentDB, in modo che sia reso permanente nel database.

Aggiungere il codice seguente alla classe DocumentDBRepository.

    public static async Task<Item> GetDocument(string id)
    {
        return await Task<Item>.Run(() =>
            Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => d.ID == id)
                .AsEnumerable()
                .FirstOrDefault());
    }

    public static async Task<Document> UpdateDocument(Item item)
    {
        var doc = Client.CreateDocumentQuery<Document>(Collection.DocumentsLink)
                    .Where(d => d.Id == item.ID)
                    .AsEnumerable().FirstOrDefault();
        
        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

Il primo dei due metodi recupera un elemento da DocumentDB, che viene
passato a ItemController e quindi alla visualizzazione Edit.

Il secondo metodo appena aggiunto sostituisce il documento in
DocumentDB con la versione del documento passata da
ItemController.

Queste sono le procedure necessarie per eseguire l'applicazione, elencare
gli elementi incompleti, aggiungere nuovi elementi e infine modificare gli elementi.

</h3>
# <a name="_Toc395637773">Esecuzione dell'applicazione in locale</a>

### 

Per eseguire il test dell'applicazione nel computer locale, premere F5 in Visual Studio.
L'app verrà compilata e verrà avviato un browser con la pagina
di griglia vuota riportata in precedenza:

![Alt text][23]

1. Usare i campi forniti per Item, Item Name e Category per l'immissione
delle informazioni, quindi fare clic sul collegamento **"Create New"** e specificare alcuni
valori. Lasciare deselezionata la casella di controllo "Completed". In caso contrario, il nuovo elemento verrà
aggiunto con uno stato completato e non sarà visualizzato nell'elenco iniziale.

	![Alt text][24]

	Se si fa clic su Create, si verrà reindirizzati alla pagina Index e
	l'elemento dovrebbe essere visualizzato nell'elenco.

	![Alt text][25]

	È possibile aggiungere altri elementi all'elenco attività.

2. Fare clic su "Edit" accanto a un elemento nell'elenco. Verrà aperta la
visualizzazione Edit, in cui è possibile aggiornare qualsiasi proprietà dell'oggetto,
incluso il flag "Completed". L'elemento sarà quindi contrassegnato come
completato e verrà rimosso dall'elenco di attività incomplete.

	![Alt text][26]

3. Per completare un'attività, è sufficiente selezionare la casella di controllo, quindi fare clic su
**Save.** Si sarà reindirizzati alla pagina di elenco e
l'elemento non sarà più incluso nell'elenco.

</h3>
# <a name="_Toc395637774">Distribuire l'applicazione in Siti Web di Azure</a>

### 

Ora che l'applicazione completa funziona correttamente con
DocumentDB, sarà possibile distribuirla in Siti Web di Azure.

Se è stata selezionata l'opzione "Ospita nel cloud" durante la creazione del progetto MVC ASP.NET
vuoto, Visual Studio semplificherà notevolmente le operazioni, eseguendo la maggior parte delle procedure
automaticamente. Per pubblicare l'applicazione è sufficiente
fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni (assicurarsi che non sia ancora
in esecuzione localmente), quindi scegliere Pubblica.

![Alt text][27]

Dovrebbe essere tutto già configurato in base alle credenziali specificate.
Il sito Web è già stato in effetti creato automaticamente in Azure all'indirizzo specificato in
"URL di destinazione". È quindi sufficiente fare clic su **Pubblica**

![Alt text][28]

Dopo alcuni secondi, Visual Studio completerà la pubblicazione dell'applicazione
Web e avvierà un browser in cui sarà possibile ammirare il proprio lavoro
in esecuzione in Azure.

</h3>
# <a name="_Toc395637775">Conclusioni</a>

### 

Congratulazioni. Si è creata la prima applicazione MVC ASP.NET usando Azure DocumentDB e la si è pubblicata in Siti Web di Azure. Il codice sorgente per l'applicazione di riferimento completa è disponibile per il download qui.

[here] (<http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409>)

  [Alt text]: ./media/documentdb-dotnet-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [Installazione guidata piattaforma Web Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
  [1]: ./media/documentdb-dotnet-application/image2.png
  [2]: ./media/documentdb-dotnet-application/image3.png
  [3]: ./media/documentdb-dotnet-application/image4.png
  [4]: ./media/documentdb-dotnet-application/image5.png
  [5]: ./media/documentdb-dotnet-application/image6.png
  [6]: ./media/documentdb-dotnet-application/image7.png
  [7]: ./media/documentdb-dotnet-application/image8.png
  [8]: ./media/documentdb-dotnet-application/image9.png
  [9]: ./media/documentdb-dotnet-application/image10.png
  [10]: ./media/documentdb-dotnet-application/image11.png
  [11]: ./media/documentdb-dotnet-application/image12.png
  [12]: ./media/documentdb-dotnet-application/image13.png
  [13]: ./media/documentdb-dotnet-application/image14.png
  [14]: ./media/documentdb-dotnet-application/image15.png
  [15]: ./media/documentdb-dotnet-application/image16.png
  [16]: ./media/documentdb-dotnet-application/image17.png
  [17]: ./media/documentdb-dotnet-application/image18.png
  [18]: ./media/documentdb-dotnet-application/image19.png
  [19]: ./media/documentdb-dotnet-application/image20.png
  [20]: ./media/documentdb-dotnet-application/image21.png
  [21]: ./media/documentdb-dotnet-application/image22.png
  [22]: ./media/documentdb-dotnet-application/image23.png
  [23]: ./media/documentdb-dotnet-application/image24.png
  [24]: ./media/documentdb-dotnet-application/image25.png
  [25]: ./media/documentdb-dotnet-application/image26.png
  [26]: ./media/documentdb-dotnet-application/image27.png
  [27]: ./media/documentdb-dotnet-application/image28.png
  [28]: ./media/documentdb-dotnet-application/image29.png
