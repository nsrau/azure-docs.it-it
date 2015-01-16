<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Creare un'applicazione Web con MVC ASP.NET usando DocumentDB | Azure" description="Informazioni sull'uso di DocumentDB per creare un'applicazione Web To Do List. È possibile usare un'applicazione Web ASP.NET MVC ospitata in Azure per archiviare e accedere ai dati."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="ryancraw" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />


<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="ryancraw" />

<a name="_Toc395809351">Build a web application with ASP.NET MVC using DocumentDB</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Overview</a>
==============================================================================================

<a name="_Toc395637759">Scenario</a>
------------------------------------

To highlight how customers can efficiently leverage Azure DocumentDB to store and query JSON documents, this document provides an end-to-end walk-through of building a ToDo List web application using Azure Document DB.

This walk-through shows you how to use DocumentDB service provided by Azure to store and access data from an ASP.NET MVC web application hosted on Azure and presumes that you have some prior experience using ASP.NET MVC and Azure Websites.

You will learn:

1\. Creating and provisioning a DocumentDB Account

2\. Creating a ASP.NET MVC Application

3\. Connecting to and using Azure DocumentDB from your web application

4\. Deploying the Web Application to Azure Websites

By following this walk-through, you will build a simple web-based task-management application that allows creating, retrieving and completing of tasks. The tasks will be stored as JSON documents in Azure DocumentDB.

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

Before following the instructions in this article, you should ensure that you have the following installed:

Git for Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (or [Visual Studio Express][] which is the free version)

Azure SDK for .NET version 2.3 or higher, available through [Microsoft Web Platform Installer][]

All the screen shots in this document have been taken using Visual Studio 2013 with Update 3 applied and Azure SDK for .NET version 2.4. If your system is configured with different versions it is possible that your screens and options won't match entirely, but if you meet the above prerequisites this solution should work.

<a name="_Toc395637761">Create a DocumentDB database account</a>
================================================================

To provision a DocumentDB database account in Azure, open the Azure Management Portal and either Click the Azure Gallery tile on the homepage or click "+" in the lower left hand corner of the screen.

![Alt text](./media/documentdb-dotnet-application/image2.png)


This will open the Azure Gallery, where you can select from the many available Azure services. In the Gallery, select "Data, storage and backup" from the list of categories.

![Alt text](./media/documentdb-dotnet-application/image3.png)

From here, select the option for Azure DocumentDB

![Alt text](./media/documentdb-dotnet-application/image4.png)


Then select "Create" from the bottom of the screen

![Alt text](./media/documentdb-dotnet-application/image5.png)

This will open up the "New DocumentDB" blade where you can specify the name, region, scale, resource group and other settings for your new account.

![Alt text](./media/documentdb-dotnet-application/image6.png)


Once you're done supplying the values for your account, click "Create" and the provisioning process will begin creating your database account.
When the provisioning process is complete, you should see a notification appear in the notifications area of the portal and the tile on your start screen (if you selected to create one) will change to show the completed action.

![Alt text](./media/documentdb-dotnet-application/image7.png)


Once provisioning is complete, clicking the DocumentDB tile from the start screen will bring up the main blade for this newly created DocumentDB account.

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


Using the "Keys" button, access your endpoint URL and the Primary Key, copy these to your clipboard and keep them handy as we will use these values in the web application we will use these values in the web application we will create next.

We will now walk through how to create a new ASP.NET MVC application from the ground-up. For your reference the complete solution can be downloaded [here].

<a name="_Toc395637762">Create a new ASP.NET MVC application</a>
================================================================

In Visual Studio, click File - New Project and select the option to create a new ASP.NET MVC Web Application.

![Alt text](./media/documentdb-dotnet-application/image10.png)


Select where you would like to create the project, what you want the project to be called (I'm using "todo") and click Ok.

![Alt text](./media/documentdb-dotnet-application/image11.png)


If you plan on hosting your application in Azure then select the box on the lower right to "Host in the cloud". We've selected to host in the cloud, and run the application hosted in an Azure Website. Selecting this option will pre-provision an Azure Website for you and make life a lot easier when it comes times to deploy the final working application. If you want to host this elsewhere or don't want to configure Azure upfront, then just un-check the "Host in the Cloud" option.

Select OK and let Visual Studio do its thing around scaffolding the empty ASP.NET MVC template. If you chose to host this in the Cloud you will see at least one additional screen asking you to login to your Azure account and provide some values for your new Website. Proceed to supply all these Azure values and continue. 

I haven't chosen a "Database server" here because we're not using an Azure SQL Database Server here, we're going to be creating a new Azure DocumentDB Account later on in the management portal. 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Once Visual Studio has finished creating the boilerplate MVC application you have an empty ASP.NET application that you can run locally.

We'll skip running locally because I'm sure we've all seen the ASP.NET "Hello World" application. Let's go straight to adding DocumentDB to this project and building our application.

</h1>
<a name="_Toc395637767">Adding DocumentDB to your project</a>
=============================================================

That takes care of most of the ASP.NET MVC plumbing that we need for
this solution. Now let's get to the real purpose of this tutorial, adding Azure DocumentDB to our web application.

### 

### <a name="_Toc395637764">Installing the NuGet package</a>

The DocumentDB .NET SDK is packaged and distributed as a NuGet package. Using the NuGet package manager in Visual Studio (which you can get to by Right-Clicking on the Project and choosing "Manage NuGet Packages"

![Alt text](./media/documentdb-dotnet-application/image21.png)

Search for Online for "Azure DocumentDB" and install the package. This will download and install the DocumentDB package as well as all dependencies, like Newtonsoft.Json.

**NOTE:** While the service is still in preview, the NuGet package is marked as "Prerelease" so you need to include the option to "Include Prerelease", else you will not find the package. 

Alternatively you could use the Package Command Console to install the package by typing;

    Install-Package Microsoft.Azure.Documents.Client -Pre

Once installed your Visual Studio solution should resemble the following with two new references added;

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">Setting up the ASP.NET MVC application</a>
==================================================================

### 

### <a name="_Toc395637764">Add a Model</a>

Let's begin by creating the **M** in MVC, the model. In Solution Explorer, right-click the *Models* folder and then click **Add**, then **Class**

![Alt text](./media/documentdb-dotnet-application/image12.png)

Name your new Class, **Item** and then add the following code in to this new Class

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

All data in DocumentDB is passed over the wire, and stored, as JSON. To control the way your objects are serialized/deserialized by JSON.NET you can use the JsonProperty attribute as demonstrated in the Item class we just created. You don't **have** to do this but i wanted to ensure that my properties followed the JSON camelCase naming conventions. 

Not only can you control the format of the property name when it goes in to JSON, but you can entirely rename your .NET properties like I did with the Description property. 

You can, if you like, use JsonConverter objects here as well to completely control how serialization is handled.  

In order to get Visual Studio to resolve the "JsonProperty" attribute used here you need to add the following using statement to the using section of your class file;

    using Newtonsoft.Json;

### <a name="_Toc395637765">Add a Controller</a>

That takes care of the M, now let's create the **C** in MVC, a controller class.
In **Solution Explorer**, right-click the *Controllers* folder and then click **Add**, then **Controller**.

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


In the **Add Scaffold** dialog box, click **MVC 5 Controller - Empty.** Click **Add.**

![Alt text](./media/documentdb-dotnet-application/image15.png)

Name your new Controller, **ItemController.**

Visual Studio will now add the ItemController your Solution Explorer should look like similar to below.

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">Add Views</a>

And finally, let's create the **V** in MVC, a view.


#### Add Item Index View

Expand the ***Views***  folder in Solution Explorer and location the (empty) Item folder which Visual Studio would've created for you when you added the *ItemController* earlier. Right click on ***Item*** and choose to Add a new View.

![Alt text](./media/documentdb-dotnet-application/image17.png)

In the "Add View" dialog. Call your view "***Index***", use the ***List*** Template, select the ***Item (todo.Models)*** which we created earlier as the class and finally use the ***~Views/Shared/_Layout.cshtml*** in the Solution as the Layout page.

![Alt text](./media/documentdb-dotnet-application/image18.png)


Once all these values are set, click Add and let Visual Studio create your view for you. Visual Studio will create a template view. Once it is done, it will open the cshtml file created. We can close this document in Visual Studio as we will come back to it later.

#### Add New Item View

In a similar fashion to above, create a new View for creating new Items as per the example shown below;

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### Add Edit Item View

<a name="_Toc395888515"></a>
============================

And finally, add one last View for editing an Item in the same way as before;

![Alt text](./media/documentdb-dotnet-application/image20.png)


Once this is done, close the cshtml documents in Visual Studio as we will return to these Views later.

</h1>
<a name="_Toc395637769">Wiring up DocumentDB</a>
------------------------------------------------

### <a name="_Toc395637770">Listing Incomplete Items</a>

Open the **ItemController** and remove all the code within the class (but leave the class) that Visual Studio added. We'll rebuild it piece by piece using DocumentDB.

Add the following code snippet within the now empty ItemController class;

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

This code also uses a "pseudo repository" class called DocumentDBRepository which we have yet to create. This is actually just a Helper class that contains all the DocumentDB specific code. For the purposes of this walk-through we aren't going to implement a full data access layer with dependency injection, and factories and repository patterns, as you would probably be doing if you were building a real world application. 
For the purposes of this walk-through we're just going to put all the data access logic directly in to one project to keep things simple and focus on the DocumentDB specific bits.

Add a new Class to your project and call it **DocumentDBRepository.**
Replace the code in the class file with the following;

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

Spend some time resolving all the namespaces in Visual Studio. All namespaces will be easy to resolve in Visual Studio as long as the NuGet package was installed successfully. The references to the ReadOrCreateDatabase and ReadOrCreateCollection methods will remain unresolved until we have added them, which comes next. 

There are two method calls used here for reading or creating DocumentDB Databases and Document Collections.
So add the following two methods to this class;

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

This takes care setting up the Database, a DocumentCollection, and creating some code to connect to DocumentDB through the DocumentClient. 

We're reading some values from configuration, so open the **web.config** and add the following lines under the
<AppSettings\> section;

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

Aprire ***App\_Start\RouteConfig.cs***, trovare la riga che inizia con "defaults:" e modificarla in modo che sia analoga a quanto riportato di seguito.

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Questo codice indica a MVC ASP.NET che, se non è stato specificato alcun valore in URL per controllare il comportamento di routing, sarà necessario usare "Item" invece di "Home" come controller e Index come visualizzazione.
Se si esegue l'applicazione, verrà effettuata una chiamata per **ItemController** e i risultati del metodo **GetIncompleteItems** verranno restituiti alla visualizzazione Views\Item\Index. 

Se lo si compila ed esegue ora, il progetto avrà un aspetto analogo al seguente.    

![Alt text](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771">Aggiunta di elementi</a>

Verranno aggiunti alcuni elementi al database, in modo che non sia disponibile solo una griglia vuota.

Sono già disponibili una visualizzazione per la creazione e un pulsante nella visualizzazione Index, che porterà l'utente alla visualizzazione Create. Sarà ora effettuata l'aggiunta di codice al controller e al repository per rendere permanente il record in DocumentDB.

Aprire il file ***ItemController.cs*** e aggiungere il frammento di codice seguente, in modo che MVC ASP.NET sappia quali operazioni eseguire per l'azione Create. In questo caso verrà semplicemente eseguito il rendering della visualizzazione Create.cshtml associata creata in precedenza.

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

**Nota sulla sicurezza**: l'attributo [ValidateAntiForgeryToken] viene usato qui per proteggere questa applicazione dagli attacchi di tipo richiesta intersito falsa. Oltre ad aggiungere questo attributo, è anche necessario che le visualizzazioni usino questo token antifalsificazione. Per altre informazioni sull'argomento ed esempi di corretta implementazione, vedere l'articolo che descrive come [prevenire le richieste intersito false][]. Nel codice sorgente, disponibile facendo clic sul collegamento per il download alla fine dell'articolo, è definita l'implementazione completa.

**Nota sulla sicurezza**: viene usato anche l'attributo [Bind] nel parametro del metodo per ottenere protezione contro gli attacchi basati su un eccesso di post. Per informazioni dettagliate, vedere l'articolo relativo alle [operazioni CRUD di base in MVC ASP.NET][]

Dopo questa operazione, il controller Items passerà   (in modo sicuro) l'elemento dal modulo al metodo CreateDocument della classe di tipo archivio. Aggiungere quindi il metodo seguente alla classe DocumentDBRepository.

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

3\.Per completare un'attività, è sufficiente selezionare la casella di controllo, quindi fare clic su **Save**. Si sarà reindirizzati alla pagina di elenco e l'elemento non sarà più incluso nell'elenco.

</h3>
<a name="_Toc395637774">Distribuire l'applicazione in Siti Web di Azure</a>
================================================================

### 

Ora che l'applicazione completa funziona correttamente con DocumentDB, sarà possibile distribuirla in Siti Web di Azure.

Se è stata selezionata l'opzione "Ospita nel cloud" durante la creazione del progetto MVC ASP.NET vuoto, Visual Studio semplificherà notevolmente le operazioni, eseguendo la maggior parte delle procedure automaticamente. Per pubblicare l'applicazione è sufficiente fare clic con il pulsante destro del mouse sul progetto in Esplora soluzioni (assicurarsi che non sia ancora in esecuzione localmente), quindi scegliere Pubblica.

![Alt text](./media/documentdb-dotnet-application/image28.png)

Dovrebbe essere tutto già configurato in base alle credenziali specificate. Il sito Web è già stato in effetti creato automaticamente in Azure all'indirizzo specificato in "URL di destinazione". È quindi sufficiente fare clic su **Pubblica**.

![Alt text](./media/documentdb-dotnet-application/image29.png)

Dopo alcuni secondi, Visual Studio completerà la pubblicazione dell'applicazione Web e avvierà un browser in cui sarà possibile ammirare il proprio lavoro in esecuzione in Azure.

</h3>



<a name="_Toc395637775">Conclusioni</a>
======================================

### 

Congratulazioni. Si è creata la prima applicazione MVC ASP.NET usando Azure DocumentDB e la si è pubblicata in Siti Web di Azure. Il codice sorgente per l'applicazione completa, inclusa la funzionalità Details & Delete che qui è stata ignorata, è disponibile per il download [qui][].


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
[Installazione guidata piattaforma Web Microsoft]: http://www.microsoft.com/web/downloads/platform.aspx
[qui]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Prevenzione delle richieste intersito false]: http://go.microsoft.com/fwlink/?LinkID=517254
[Operazioni CRUD di base in MVC ASP.NET]: http://go.microsoft.com/fwlink/?LinkId=317598
