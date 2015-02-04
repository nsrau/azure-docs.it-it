<properties urlDisplayName="Website with MongoDB VM" pageTitle="Sito Web .NET con MongoDB in una macchina virtuale - Azure" metaKeywords="Azure Git ASP.NET MongoDB, Git .NET, Git MongoDB, ASP.NET MongoDB, Azure MongoDB, Azure ASP.NET, Azure tutorial" description="Un'esercitazione che illustra come usare Git per distribuire un'app ASP.NET in un sito Web di Azure connessa a MongoDB in una macchina virtuale." metaCanonical="" services="web-sites,virtual-machines" documentationCenter=".NET" title="Create an Azure website that connects to MongoDB running on a virtual machine in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/24/2014" ms.author="cephalin" />


# Creare un sito Web di Azure che si connette a MongoDB in esecuzione su una macchina virtuale in Azure

Con Git è possibile distribuire un'applicazione ASP.NET in un sito Web di Azure. In questa esercitazione verrà creata una semplice applicazione di elenco di attività ASP.NET MVC front-end che si connette a un database MongoDB in esecuzione in una macchina virtuale in Azure.  [MongoDB][MongoDB] è un diffuso database NoSQL open source a prestazioni elevate. Dopo aver eseguito e verificato l'applicazione ASP.NET sul computer di sviluppo, l'applicazione verrà caricata in un sito Web di Azure usando Git.

[WACOM.INCLUDE [create-account-and-websites-and-vms-note](../includes/create-account-and-websites-and-vms-note.md)]



##Panoramica##

In questa esercitazione si apprenderà come:

- [Creare una macchina virtuale e installare MongoDB](#virtualmachine)
- [Creare ed eseguire l'applicazione ASP.NET My Task List sul computer di sviluppo](#createapp)
- [Creare un sito Web di Azure](#createwebsite)
- [Distribuire l'applicazione ASP.NET nel sito Web usando Git](#deployapp)


##Informazioni di background##

Per questa esercitazione è utile, ma non obbligatorio, disporre di conoscenze sui seguenti argomenti:

* Driver C# per MongoDB. Per altre informazioni sull'uso di MongoDB per lo sviluppo di applicazioni C#, vedere il [centro linguistico CSharp di MongoDB][MongoC#LangCenter]. 
* Framework applicazione Web ASP.NET. Informazioni complete sull'argomento sono disponibili nel [sito Web ASP.net][ASP.NET].
* Framework applicazione Web ASP.NET MVC. Informazioni complete sull'argomento sono disponibili nel [sito Web MVC ASP.NET][MVCWebSite].
* Azure. Per informazioni, visitare il sito Web di [Azure][WindowsAzure].


##Operazioni preliminari##

In questa sezione si apprenderà come creare una macchina virtuale in Azure e installare MongoDB, nonché configurare l'ambiente di sviluppo.

<a id="virtualmachine"></a> 
###Creare una macchina virtuale e installare MongoDB##

In questa esercitazione si presuppone che l'utente abbia creato una macchina virtuale in Azure. Dopo aver creato la macchina virtuale è necessario installarvi MongoDB:

* Per creare una macchina virtuale di Windows e installare MongoDB, vedere [Installazione di MongoDB in una macchina virtuale che esegue Windows Server in Azure][InstallMongoOnWindowsVM].
* In alternativa, per creare una macchina virtuale Linux e installare MongoDB, visualizzare [Installazione di MongoDB in una macchina virtuale che esegue CentOS Linux in Azure][InstallMongoOnCentOSLinuxVM].

Dopo aver creato la macchina virtuale in Azure e installato MongoDB, prendere nota del nome DNS della macchina virtuale (ad esempio; "testlinuxvm.cloudapp.net") e della porta esterna per MongoDB specificata nell'endpoint.  Sarà necessario usare queste informazioni più avanti nell'esercitazione.

### Installazione di Visual Studio##

Iniziare installando ed eseguendo [Visual Studio Express 2013 per il Web] [VSEWeb] o [Visual Studio 2013] [VSUlt].

Visual Studio è un IDE, cioè un ambiente di sviluppo integrato. Così come Microsoft Word viene usato per scrivere documenti, IDE viene usato per creare applicazioni. In questa esercitazione si utilizza Microsoft Visual Studio 2013, ma è possibile usare Microsoft Visual Studio Express 2013, che è una versione gratuita di Microsoft Visual Studio.

<a id="createapp"></a>
##Creare ed eseguire l'applicazione ASP.NET My Task List sul computer di sviluppo##

In questa sezione verrà creata un'applicazione ASP.NET denominata "My Task List" usando Visual Studio.  L'applicazione verrà eseguita localmente, ma si connetterà alla macchina virtuale in Azure e utilizzerà l'istanza MongoDB creata in questa esercitazione.

###Creazione dell'applicazione##
In Visual Studio, fare clic su **Nuovo progetto**.

![Start Page New Project][StartPageNewProject]

Nel riquadro sinistro della finestra **Nuovo progetto**, selezionare **Visual C#**, quindi selezionare **Web**. Nel riquadro centrale, selezionare **Applicazione Web ASP.NET**. In basso, assegnare al progetto il nome "MyTaskListApp", quindi fare clic su **OK**.

![New Project Dialog][NewProjectMyTaskListApp]

Nella finestra di dialogo **Nuovo progetto ASP.NET**, selezionare **MVC**, quindi fare clic su **OK**.

![Select MVC Template][VS2013SelectMVCTemplate]

Al termine della creazione del progetto, viene visualizzata la pagina predefinita creata dal template.

![Default ASP.NET MVC Application][VS2013DefaultMVCApplication]

###Installazione del driver MongoDB C#

MongoDB offre il supporto lato client per le applicazioni C# tramite un driver, che è necessario installare nel computer di sviluppo locale. Il driver C# è disponibile tramite NuGet.

Per installare il driver MongoDB C#:

1. In **Esplora soluzioni**, sotto il progetto **MyTaskListApp**, fare clic con il pulsante destro del mouse su **Riferimenti** e selezionare **Gestisci pacchetti NuGet**.

	![Manage NuGet Packages][VS2013ManageNuGetPackages]

2. Nel riquadro sinistro della finestra **Gestisci pacchetti NuGet** fare clic su **Online**. Nella casella di **ricerca online** a destra, digitare "mongocsharpdriver".  Fare clic su **Installa** per installare il driver.

	![Search for MongoDB C# Driver][SearchforMongoDBCSharpDriver]

3. Fare clic su **Accetto** per accettare i termini di licenza di 10gen, Inc.

4. Dopo aver installato il driver, fare clic su **Chiudi**.
	![MongoDB C# Driver Installed][MongoDBCsharpDriverInstalled]


Il driver MongoDB C# è ora installato.  I riferimenti alle librerie **MongoDB.Driver.dll** e **MongoDB.Bson.dll** sono stati aggiunti al progetto.

![MongoDB C# Driver References][MongoDBCSharpDriverReferences]

###Aggiunta di un modello##
In **Esplora soluzioni**, fare clic con il pulsante destro del mouse sulla cartella *Modelli*, quindi scegliere **Aggiungi**, **Classe** per aggiungere una nuova classe e denominarla *TaskModel.cs*.  In *TaskModel.cs*, sostituire il codice esistente con il codice seguente:

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

###Aggiunta di un livello di accesso ai dati###
In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto *MyTaskListApp*, quindi scegliere **Aggiungi**, **Nuova cartella** e scegliere la cartella denominata *DAL*.  Fare clic con il pulsante destro del mouse sulla cartella *DAL* e scegliere **Aggiungi**, **Classe** per aggiungere una nuova classe. Assegnare al file della classe il nome *Dal.cs*.  In *Dal.cs* sostituire il codice esistente con il codice seguente:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MyTaskListApp.Models;
	using MongoDB.Driver;
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
	        private string connectionString = "mongodb://<vm-dns-name>";
	
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
	                MongoCollection<MyTask> collection = GetTasksCollection();
	                return collection.FindAll().ToList<MyTask>();
	            }
	            catch (MongoConnectionException)
	            {
	                return new List<MyTask >();
	            }
	        }
	
	        // Creates a Task and inserts it into the collection in MongoDB.
	        public void CreateTask(MyTask task)
	        {
	            MongoCollection<MyTask> collection = GetTasksCollectionForEdit();
	            try
	            {
	                collection.Insert(task, SafeMode.True);
	            }
	            catch (MongoCommandException ex)
	            {
	                string msg = ex.Message;
	            }
	        }
	
	        private MongoCollection<MyTask> GetTasksCollection()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        private MongoCollection<MyTask> GetTasksCollectionForEdit()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
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

###Aggiunta di un controller###
In **Esplora soluzioni** aprire il file *Controllers\HomeController.cs* e sostituirne il contenuto con quello riportato di seguito:

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

###Impostazione dello stile del sito###
Per modificare il titolo nella parte superiore della pagina, in **Esplora soluzioni** aprire il file Views\Shared\_Layout.cshtml e sostituire "Application name" nell'intestazione della barra di navigazione con "My Task List Application":

 	@Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Per impostare il menu Elenco attività, aprire il file *\Views\Home\Index.cshtml* e sostituire il codice esistente con il seguente codice:
	
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


Per aggiungere la possibilità di creare una nuova attività, fare clic con il pulsante destro del mouse sulla cartella *Views\Home\\*, quindi scegliere **Aggiungi**, **Visualizzazione** per aggiungere una nuova visualizzazione. Assegnare alla visualizzazione il nome *Create*. Sostituire il codice con il codice seguente:

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

![Solution Explorer][SolutionExplorerMyTaskListApp]

###Impostazione della stringa di connessione MongoDB##
In **Esplora soluzioni** aprire il file *DAL/Dal.cs*. Individuare la riga di codice seguente:

	private string connectionString = "mongodb://<vm-dns-name>";

Sostituire `<vm-dns-name>` con il nome DNS della macchina virtuale che esegue MongoDB creata nel passaggio Creare una macchina virtuale e installare [MongoDB][] di questa esercitazione.  Per individuare il nome DNS della macchina virtuale, passare al portale di gestione di Azure, selezionare **Macchine virtuali** e individuare **Nome DN**.

Se il nome DNS della macchina virtuale è "testlinuxvm.cloudapp.net" e MongoDB è in ascolto sulla porta predefinita 27017, la riga di codice della stringa di connessione avrà il seguente aspetto:

	private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Se l'endpoint della macchina virtuale specifica una porta esterna diversa per MongoDB, è possibile specificare la porta nella stringa di connessione:

 	private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Per altre informazioni sulle stringhe di connessione di MongoDB, vedere l'argomento relativo alle [connessioni][MongoConnectionStrings].

###Test della distribuzione locale###

Per eseguire l'applicazione sul computer di sviluppo, selezionare **Avvia debug** dal menu **Debug** oppure premere **F5**. IIS Express viene avviato e nel browser viene avviata la home page dell'applicazione.  È possibile aggiungere una nuova attività, che verrà aggiunta al database MongoDB in esecuzione sulla macchina virtuale in Azure.

![My Task List Application][TaskListAppBlank]

<h2>Distribuire un'applicazione Web ASP.NET in un sito Web di Azure</h2>

In questa sezione verrà creato un sito Web e verrà distribuita l'applicazione ASP.NET My Task List usando Git.

<a id="createwebsite"></a> 
###Creare un sito Web di Azure###
In questa sezione verrà creato un sito Web di Azure.

1. Aprire un Web browser e passare al [portale di gestione di Azure][AzurePortal]. Accedere con l'account Azure. 
2. Fare clic su **+Nuovo** nella parte inferiore della pagina, quindi selezionare **Sito Web** e infine **Creazione rapida**.
3. Immettere un prefisso univoco per l'URL dell'applicazione.
4. Scegliere un'area,
5. Fare clic su **Crea sito Web**.

![Create a new web site][WAWSCreateWebSite]

6. Il sito Web verrà creato in modo rapido e visualizzato in **Siti Web**.

![WAWSDashboardMyTaskListApp][WAWSDashboardMyTaskListApp]

<a id="deployapp"></a> 
###Distribuire l'applicazione ASP.NET nel sito Web usando Git
In questa sezione verrà distribuita l'applicazione ASP.NET My Task List usando Git.

1. Fare clic sul nome del sito Web in **Siti Web**, quindi selezionare **Dashboard**.  A destra, sotto riepilogo rapido, fare clic su **Imposta distribuzione dal controllo del codice sorgente**.
2. Nella pagina **Dove è il codice sorgente?** scegliere **Repository Git locale**, quindi fare clic sulla freccia **Avanti**. 
3. Il repository Git verrà creato rapidamente. Prendere nota delle istruzioni riportate nella pagina visualizzata, in quanto verranno usate nella sezione successiva.

	![Git Repository is Ready][Image9]

4. In **Esegui push dei file locali in Azure** sono disponibili istruzioni per il push del codice in Azure. Le istruzioni avranno un aspetto simile al seguente:

	![Push local files to Azure][Image10]
	
5. Se Git non è ancora installato, installarlo usando il link **Scaricalo qui** riportato al passaggio 1.
6. Seguendo le istruzioni riportate al passaggio 2, eseguire il commit dei file locali.  
7. Aggiungere il repository Azure remoto ed eseguire il push dei file nel sito Web di Azure seguendo le istruzioni riportate nel passaggio 3.
8. Al termine della distribuzione verrà visualizzata la seguente conferma:

	![Deployment Complete][Image11]

9. Il sito Web di Azure è ora disponibile.  Nella pagina **Dashboard** controllare la presenza del sito e il campo **URL sito** per trovare l'URL del sito. Se sono state seguite le procedure di questa esercitazione, il sito dovrebbe essere disponibile all'URL: http://mytasklistapp.azurewebsites.net.

##Riepilogo##

L'applicazione ASP.NET è stata correttamente distribuita in un sito Web di Azure.  Per visualizzare il sito, fare clic sul link riportato nel campo **URL sito** della pagina **Dashboard**. Per altre informazioni sull'uso di MongoDB per lo sviluppo di applicazioni C#, vedere il [centro linguistico CSharp][MongoC#LangCenter]. 


<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnCentOSLinuxVM]: /it-it/manage/linux/common-tasks/mongodb-on-a-linux-vm/
[InstallMongoOnWindowsVM]: /it-it/manage/windows/common-tasks/install-mongodb/
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
[Creare ed eseguire l'applicazione ASP.NET My Task List sul computer di sviluppo]: #createapp
[Creare un sito Web di Azure]: #createwebsite
[Distribuire l'applicazione ASP.NET nel sito Web usando Git]: #deployapp

<!--HONumber=35.1-->
