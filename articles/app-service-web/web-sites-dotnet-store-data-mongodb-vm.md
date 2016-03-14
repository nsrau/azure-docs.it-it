<properties 
	pageTitle="Creare un sito Web di Azure che si connette a MongoDB in esecuzione su una macchina virtuale in Azure" 
	description="Un'esercitazione che illustra come usare Git per distribuire un'app ASP.NET in un sito Web di Azure connessa a MongoDB in una macchina virtuale."
	tags="azure-portal" 
	services="app-service\web, virtual-machines" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="cephalin"/>


# Creare un sito Web di Azure che si connette a MongoDB in esecuzione su una macchina virtuale in Azure

Utilizzando Git è possibile distribuire un'applicazione ASP.NET in un sito Web di Azure. In questa esercitazione verrà creata una semplice applicazione di elenco di attività ASP.NET MVC front-end che si connette a un database MongoDB in esecuzione in una macchina virtuale in Azure. [MongoDB][MongoDB] è un diffuso database NoSQL open source a prestazioni elevate. Dopo aver eseguito e verificato l'applicazione ASP.NET sul computer di sviluppo, l'applicazione verrà caricata in un sito Web di Azure utilizzando Git.

>[AZURE.NOTE] Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.


## Informazioni di background ##

Per questa esercitazione è utile, ma non obbligatorio, disporre di conoscenze sui seguenti argomenti:

* Driver C# per MongoDB. Per ulteriori informazioni sull'utilizzo di MongoDB per lo sviluppo di applicazioni C#, vedere il [centro linguistico CSharp][MongoC#LangCenter] di MongoDB. 
* Framework applicazione Web ASP.NET. Informazioni complete sull'argomento sono disponibili nel [sito Web ASP.net][ASP.NET].
* Framework applicazione Web ASP.NET MVC. Informazioni complete sull'argomento sono disponibili nel [sito Web MVC ASP.NET][MVCWebSite].
* Azure. Per informazioni, visitare il sito Web di [Azure][WindowsAzure].

## Prerequisiti ##

- [Visual Studio Express 2013 per Web][VSEWeb] o [Visual Studio 2013][VSUlt]
- [Azure SDK per .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
- Una sottoscrizione di Microsoft Azure

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a>
## Creare una macchina virtuale e installare MongoDB ##

In questa esercitazione si presuppone che l'utente abbia creato una macchina virtuale in Azure. Dopo aver creato la macchina virtuale è necessario installarvi MongoDB:

* Per creare una macchina virtuale di Windows e installare MongoDB, vedere [Installazione di MongoDB in una macchina virtuale che esegue Windows Server in Azure][InstallMongoOnWindowsVM].


Dopo aver creato la macchina virtuale in Azure e installato MongoDB, prendere nota del nome DNS della macchina virtuale (ad esempio; "testlinuxvm.cloudapp.net") e della porta esterna per MongoDB specificata nell'endpoint. Sarà necessario utilizzare queste informazioni più avanti nell'esercitazione.

<a id="createapp"></a>
## Creazione dell'applicazione ##

In questa sezione verranno creare un'applicazione ASP.NET denominata "My Task List" utilizzando Visual Studio e verrà eseguita una distribuzione iniziale in Azure applicazione servizio Web Apps. L'applicazione verrà eseguita localmente, ma si connetterà alla macchina virtuale in Azure e utilizzerà l'istanza MongoDB creata in questa esercitazione.

1. In Visual Studio, fare clic su **Nuovo progetto**.

	![Pagina iniziale Nuovo progetto][StartPageNewProject]

1. Nel riquadro sinistro della finestra **Nuovo progetto**, selezionare **Visual C#**, quindi selezionare **Web**. Nel riquadro centrale, selezionare **Applicazione Web ASP.NET**. In basso, assegnare al progetto il nome "MyTaskListApp", quindi fare clic su **OK**.

	![Finestra di dialogo Nuovo progetto][NewProjectMyTaskListApp]

1. Nella finestra di dialogo **Nuovo progetto ASP.NET**, selezionare **MVC**, quindi fare clic su **OK**.

	![Selezione di un template MVC][VS2013SelectMVCTemplate]

1. Se non è già stata effettuata in Microsoft Azure, verrà richiesto di accedere. Seguire le istruzioni per accedere a Azure.
2. Una volta che si sono connessi, è possibile avviare la configurazione dell'applicazione web servizio App. Specificare il **nome dell'app Web**, **piano di servizio App**, **gruppo di risorse** e **area**, quindi fare clic su **Crea**.

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)

1. Al termine della creazione del progetto, di attesa per l'applicazione web da creare nel servizio App Azure come indicato nella **attività servizio App Azure** finestra. Fare clic su **MyTaskListApp pubblica per questa applicazione Web ora**.

1. Fare clic su **Pubblica**.

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)

	Una volta pubblicata l'applicazione ASP.NET predefinita in Azure applicazione servizio Web Apps, questa verrà avviata nel browser.

## Installazione del driver MongoDB C#

MongoDB offre il supporto lato client per le applicazioni C# tramite un driver, che è necessario installare nel computer di sviluppo locale. Il driver C# è disponibile tramite NuGet.

Per installare il driver MongoDB C#:

1. In **Esplora soluzioni**, sotto il progetto **MyTaskListApp** e selezionare **Gestisci pacchetti NuGet**

	![Manage NuGet Packages][VS2013ManageNuGetPackages]

2. Nel riquadro sinistro della finestra **Gestisci pacchetti NuGet** fare clic su **Online**. Nella casella di **ricerca online** a destra, digitare "mongodb.driver". Fare clic su **Installa** per installare il driver.

	![Ricerca del driver MongoDB C#][SearchforMongoDBCSharpDriver]

3. Fare clic su **Accetto** per accettare i termini di licenza di 10gen, Inc.

4. Dopo aver installato il driver, fare clic su **Chiudi**. ![Driver MongoDB C# installato][MongoDBCsharpDriverInstalled]


Il driver MongoDB C# è ora installato. I riferimenti alle librerie **MongoDB.Bson**, **MongoDB.Driver**, e **MongoDB.Driver.Core** sono stati aggiunti al progetto.

![Riferimenti al driver MongoDB C#][MongoDBCSharpDriverReferences]

## Aggiunta di un modello ##
In **Esplora soluzioni**, fare clic con il pulsante destro del mouse sulla cartella *Modelli*, quindi scegliere **Aggiungi**, **Classe** per aggiungere una nuova classe e denominarla *TaskModel.cs*. In *TaskModel.cs*, sostituire il codice esistente con il codice seguente:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MongoDB.Bson.Serialization.Attributes;
	using MongoDB.Bson.Serialization.IdGenerators;
	using MongoDB.Bson;
	
	namespace MyTaskListApp.Models
	{
	    public class MyTask
	    {
	        [BsonId(IdGenerator = typeof(CombGuidGenerator))]
	        public Guid Id { get; set; }
	
	        [BsonElement("Name")]
	        public string Name { get; set; }
	
	        [BsonElement("Category")]
	        public string Category { get; set; }
	
	        [BsonElement("Date")]
	        public DateTime Date { get; set; }
	
	        [BsonElement("CreatedDate")]
	        public DateTime CreatedDate { get; set; }
	
	    }
	}

## Aggiunta di un livello di accesso ai dati ##
In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto *MyTaskListApp*, quindi scegliere **Aggiungi**, **Nuova cartella** e scegliere la cartella denominata *DAL*. Fare clic con il pulsante destro del mouse sulla cartella *DAL* e scegliere **Aggiungi**, **Classe** per aggiungere una nuova classe. Assegnare al file della classe il nome *Dal.cs*. In *Dal.cs* sostituire il codice esistente con il codice seguente:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MyTaskListApp.Models;
	using MongoDB.Driver;
	using MongoDB.Bson;
	using System.Configuration;
	
	
	namespace MyTaskListApp
	{
	    public class Dal : IDisposable
	    {
	        private MongoServer mongoServer = null;
	        private bool disposed = false;
	
	        // To do: update the connection string with the DNS name
	        // or IP address of your server. 
	        //For example, "mongodb://testlinux.cloudapp.net"
	        private string connectionString = "mongodb://mongodbsrv20151211.cloudapp.net";
	
	        // This sample uses a database named "Tasks" and a 
	        //collection named "TasksList".  The database and collection 
	        //will be automatically created if they don't already exist.
	        private string dbName = "Tasks";
	        private string collectionName = "TasksList";
	
	        // Default constructor.        
	        public Dal()
	        {
	        }
	
	        // Gets all Task items from the MongoDB server.        
	        public List<MyTask> GetAllTasks()
	        {
	            try
	            {
	                var collection = GetTasksCollection();
	                return collection.Find(new BsonDocument()).ToList();
	            }
	            catch (MongoConnectionException)
	            {
	                return new List<MyTask>();
	            }
	        }
	
	        // Creates a Task and inserts it into the collection in MongoDB.
	        public void CreateTask(MyTask task)
	        {
	            var collection = GetTasksCollectionForEdit();
	            try
	            {
	                collection.InsertOne(task);
	            }
	            catch (MongoCommandException ex)
	            {
	                string msg = ex.Message;
	            }
	        }
	
	        private IMongoCollection<MyTask> GetTasksCollection()
	        {
	            MongoClient client = new MongoClient(connectionString);
	            var database = client.GetDatabase(dbName);
	            var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        private IMongoCollection<MyTask> GetTasksCollectionForEdit()
	        {
	            MongoClient client = new MongoClient(connectionString);
	            var database = client.GetDatabase(dbName);
	            var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        # region IDisposable
	
	        public void Dispose()
	        {
	            this.Dispose(true);
	            GC.SuppressFinalize(this);
	        }
	
	        protected virtual void Dispose(bool disposing)
	        {
	            if (!this.disposed)
	            {
	                if (disposing)
	                {
	                    if (mongoServer != null)
	                    {
	                        this.mongoServer.Disconnect();
	                    }
	                }
	            }
	
	            this.disposed = true;
	        }
	
	        # endregion
	    }
	}

## Aggiunta di un controller ##
In *Esplora soluzioni* aprire il file **Controllers\\HomeController.cs** e sostituirne il contenuto con quello riportato di seguito:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.Mvc;
	using MyTaskListApp.Models;
	using System.Configuration;
	
	namespace MyTaskListApp.Controllers
	{
	    public class HomeController : Controller, IDisposable
	    {
	        private Dal dal = new Dal();
	        private bool disposed = false;
	        //
	        // GET: /MyTask/
	
	        public ActionResult Index()
	        {
	            return View(dal.GetAllTasks());
	        }
	
	        //
	        // GET: /MyTask/Create
	
	        public ActionResult Create()
	        {
	            return View();
	        }
	
	        //
	        // POST: /MyTask/Create
	
	        [HttpPost]
	        public ActionResult Create(MyTask task)
	        {
	            try
	            {
	                dal.CreateTask(task);
	                return RedirectToAction("Index");
	            }
	            catch
	            {
	                return View();
	            }
	        }
	
	        public ActionResult About()
	        {
	            return View();
	        }
	
	        # region IDisposable
	
	        new protected void Dispose()
	        {
	            this.Dispose(true);
	            GC.SuppressFinalize(this);
	        }
	
	        new protected virtual void Dispose(bool disposing)
	        {
	            if (!this.disposed)
	            {
	                if (disposing)
	                {
	                    this.dal.Dispose();
	                }
	            }
	
	            this.disposed = true;
	        }
	
	        # endregion
	
	    }
	}

## Impostare gli stili ##
Per modificare il titolo nella parte superiore della pagina, in **Esplora soluzioni** aprire il file Views\\Shared\\_Layout.cshtml e sostituire "Application name" nell'intestazione della barra di navigazione con "My Task List Application":

 	@Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Per impostare il menu Elenco attività, aprire il file *\\Views\\Home\\Index.cshtml* e sostituire il codice esistente con il seguente codice:
	
	@model IEnumerable<MyTaskListApp.Models.MyTask>
	
	@{
	    ViewBag.Title = "My Task List";
	}
	
	<h2>My Task List</h2>
	
	<table border="1">
	    <tr>
	        <th>Task</th>
	        <th>Category</th>
	        <th>Date</th>
	        
	    </tr>
	
	@foreach (var item in Model) {
	    <tr>
	        <td>
	            @Html.DisplayFor(modelItem => item.Name)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Category)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Date)
	        </td>
	        
	    </tr>
	}
	
	</table>
	<div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


Per aggiungere la possibilità di creare una nuova attività, fare clic con il pulsante destro del mouse sulla cartella *Views\\Home\*, quindi scegliere **Aggiungi**, **Visualizzazione** per aggiungere una nuova visualizzazione. Assegnare alla visualizzazione il nome *Crea*. Sostituire il codice con il codice seguente:

	@model MyTaskListApp.Models.MyTask
	
	<script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
	
	@using (Html.BeginForm("Create", "Home")) {
	    @Html.ValidationSummary(true)
	    <fieldset>
	        <legend>New Task</legend>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Name)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Name)
	            @Html.ValidationMessageFor(model => model.Name)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Category)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Category)
	            @Html.ValidationMessageFor(model => model.Category)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Date)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Date)
	            @Html.ValidationMessageFor(model => model.Date)
	        </div>
	
	        <p>
	            <input type="submit" value="Create" />
	        </p>
	    </fieldset>
	}

L'aspetto di **Esplora soluzioni** dovrebbe risultare simile al seguente:

![Esplora soluzioni][SolutionExplorerMyTaskListApp]

## Impostazione della stringa di connessione MongoDB ##
In **Esplora soluzioni** aprire il file *DAL/Dal.cs*. Individuare la riga di codice seguente:

	private string connectionString = "mongodb://<vm-dns-name>";

Sostituire `<vm-dns-name>` con il nome DNS della macchina virtuale che esegue MongoDB creata nel passaggio [Creare una macchina virtuale e installare MongoDB][] di questa esercitazione. Per individuare il nome DNS della macchina virtuale, passare al portale di gestione di Azure, selezionare **Macchine virtuali** e individuare **Nome DN**.

Se il nome DNS della macchina virtuale è "testlinuxvm.cloudapp.net" e MongoDB è in ascolto sulla porta predefinita 27017, la riga di codice della stringa di connessione avrà il seguente aspetto:

	private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Se l'endpoint della macchina virtuale specifica una porta esterna diversa per MongoDB, è possibile specificare la porta nella stringa di connessione:

 	private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Per altre informazioni sulle stringhe di connessione di MongoDB, vedere l'argomento relativo alle [connessioni][MongoConnectionStrings].

## Test della distribuzione locale ##

Per eseguire l'applicazione sul computer di sviluppo, selezionare **Avvia debug** dal menu **Debug** oppure premere **F5**. IIS Express viene avviato e nel browser viene avviata la home page dell'applicazione. È possibile aggiungere una nuova attività, che verrà aggiunta al database MongoDB in esecuzione sulla macchina virtuale in Azure.

![Applicazione My Task List][TaskListAppBlank]

## Pubblicare nelle App Web di Azure App Service

In questa sezione verrà pubblicato le modifiche in Azure applicazione servizio Web Apps.

1. In Esplora soluzioni, fare clic **MyTaskListApp** nuovamente e fare clic su **Pubblica**.
2. Fare clic su **Pubblica**.

	Si noterà ora l'applicazione web in esecuzione in Azure App servizio e l'accesso al database MongoDB in macchine virtuali di Azure.

## Riepilogo ##

L'applicazione ASP.NET è stata correttamente distribuita in un sito Web di Azure. Per visualizzare l'applicazione web:

1. Accedere al Portale di Azure.
2. Fare clic su **App Web**. 
3. Selezionare l'applicazione web nel **applicazioni Web** elenco.

Per ulteriori informazioni sull'utilizzo di MongoDB per lo sviluppo di applicazioni C#, vedere il [centro linguistico CSharp][MongoC#LangCenter] di MongoDB.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnWindowsVM]: ../virtual-machines/virtual-machines-install-mongodb-windows-server.md
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[Creare una macchina virtuale e installare MongoDB]: #virtualmachine
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp
 

<!---HONumber=AcomDC_0302_2016-->