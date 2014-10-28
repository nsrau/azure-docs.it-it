<properties linkid="develop-dotnet-website-with-mongodb-vm" urlDisplayName="Website with MongoDB VM" pageTitle=".NET website with MongoDB on a virtual machine - Azure" metaKeywords="Azure Git ASP.NET MongoDB, Git .NET, Git MongoDB, ASP.NET MongoDB, Azure MongoDB, Azure ASP.NET, Azure tutorial" description="A tutorial that teaches you how to use Git to deploy an ASP.NET app to an Azure website connected to MongoDB on a virtual machine." metaCanonical="" services="web-sites,virtual-machines" documentationCenter=".NET" title="Create an Azure website that connects to MongoDB running on a virtual machine in Azure" authors="cephalin" solutions="" manager="wpickett" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin"></tags>

# Creare un sito Web di Azure che si connette a MongoDB in esecuzione su una macchina virtuale in Azure

Con Git è possibile distribuire un'applicazione ASP.NET in un sito Web di Azure. In questa esercitazione verrà creata una semplice applicazione di elenco di attività ASP.NET MVC front-end che si connette a un database MongoDB in esecuzione in una macchina virtuale in Azure. [MongoDB][MongoDB] è un diffuso database NoSQL open source a prestazioni elevate. Dopo aver eseguito e verificato l'applicazione ASP.NET sul computer di sviluppo, l'applicazione verrà caricata in un sito Web di Azure usando Git.

[WACOM.INCLUDE [create-account-and-websites-and-vms-note][create-account-and-websites-and-vms-note]]

## Panoramica

In questa esercitazione si apprenderà come:

-   [Creare una macchina virtuale e installare MongoDB][Creare una macchina virtuale e installare MongoDB]
-   [Creare ed eseguire l'applicazione ASP.NET My Task List sul computer di sviluppo][Creare ed eseguire l'applicazione ASP.NET My Task List sul computer di sviluppo]
-   [Creare un sito Web di Azure][Creare un sito Web di Azure]
-   [Distribuire l'applicazione ASP.NET nel sito Web usando Git][Distribuire l'applicazione ASP.NET nel sito Web usando Git]

## Informazioni di background

Per questa esercitazione è utile, ma non obbligatorio, disporre di conoscenze sui seguenti argomenti:

-   Driver C# per MongoDB. Per altre informazioni sull'utilizzo di MongoDB per lo sviluppo di applicazioni C#, vedere il [centro linguistico CSharp][centro linguistico CSharp] di MongoDB.
-   Framework applicazione Web ASP.NET. Informazioni complete sull'argomento sono disponibili nel [sito Web ASP.net][sito Web ASP.net].
-   Framework applicazione Web ASP.NET MVC. Informazioni complete sull'argomento sono disponibili nel [sito Web MVC ASP.NET][sito Web MVC ASP.NET].
-   Azure. Per informazioni, visitare il sito Web di [Azure][Azure].

## Operazioni preliminari

In questa sezione si apprenderà come creare una macchina virtuale in Azure e installare MongoDB, nonché configurare l'ambiente di sviluppo.

<span id="virtualmachine"></span></a>

### Creare una macchina virtuale e installare MongoDB

In questa esercitazione si presuppone che l'utente abbia creato una macchina virtuale in Azure. Dopo aver creato la macchina virtuale è necessario installarvi MongoDB:

-   Per creare una macchina virtuale di Windows e installare MongoDB, vedere [Installazione di MongoDB in una macchina virtuale che esegue Windows Server in Azure][Installazione di MongoDB in una macchina virtuale che esegue Windows Server in Azure].
-   In alternativa, per creare una macchina virtuale Linux e installare MongoDB, visualizzare [Installazione di MongoDB in una macchina virtuale che esegue CentOS Linux in Azure][Installazione di MongoDB in una macchina virtuale che esegue CentOS Linux in Azure].

Dopo aver creato la macchina virtuale in Azure e installato MongoDB, prendere nota del nome DNS della macchina virtuale (ad esempio; "testlinuxvm.cloudapp.net") e della porta esterna per MongoDB specificata nell'endpoint. Sarà necessario usare queste informazioni più avanti nell'esercitazione.

### Installazione di Visual Studio

Iniziare installando ed eseguendo [Visual Studio Express 2013 per il Web][Visual Studio Express 2013 per il Web] o [Visual Studio 2013][Visual Studio 2013].

Visual Studio è un IDE, cioè un ambiente di sviluppo integrato. Così come Microsoft Word viene usato per scrivere documenti, IDE viene usato per creare applicazioni. In questa esercitazione si utilizza Microsoft Visual Studio 2013, ma è possibile usare Microsoft Visual Studio Express 2013, che è una versione gratuita di Microsoft Visual Studio.

<span id="createapp"></span></a>

## Creare ed eseguire l'applicazione ASP.NET My Task List sul computer di sviluppo

In questa sezione verrà creata un'applicazione ASP.NET denominata "My Task List" usando Visual Studio. L'applicazione verrà eseguita localmente, ma si connetterà alla macchina virtuale in Azure e utilizzerà l'istanza MongoDB creata in questa esercitazione.

### Creazione dell'applicazione

In Visual Studio, fare clic su **Nuovo progetto**.

![Pagina iniziale Nuovo progetto][Pagina iniziale Nuovo progetto]

Nel riquadro sinistro della finestra **Nuovo progetto**, selezionare **Visual C#**, quindi selezionare **Web**. Nel riquadro centrale, selezionare **Applicazione Web ASP.NET**. In basso, assegnare al progetto il nome "MyTaskListApp", quindi fare clic su **OK**.

![Finestra di dialogo Nuovo progetto][Finestra di dialogo Nuovo progetto]

Nella finestra di dialogo **Nuovo progetto ASP.NET**, selezionare **MVC**, quindi fare clic su **OK**.

![Selezione di un template MVC][Selezione di un template MVC]

Al termine della creazione del progetto, viene visualizzata la pagina predefinita creata dal template.

![Applicazione ASP.NET MVC predefinita][Applicazione ASP.NET MVC predefinita]

### Installazione del driver MongoDB C#

MongoDB offre il supporto lato client per le applicazioni C# tramite un driver, che è necessario installare nel computer di sviluppo locale. Il driver C# è disponibile tramite NuGet.

Per installare il driver MongoDB C#:

1.  In **Esplora soluzioni**, sotto il progetto **MyTaskListApp**, fare clic con il pulsante destro del mouse su **Riferimenti** e selezionare **Gestisci pacchetti NuGet**.

    ![Manage NuGet Packages][Manage NuGet Packages]

2.  Nel riquadro sinistro della finestra **Gestisci pacchetti NuGet** fare clic su **Online**. Nella casella di **ricerca online** a destra, digitare "mongocsharpdriver". Fare clic su **Installa** per installare il driver.

    ![Ricerca del driver MongoDB C#][Ricerca del driver MongoDB C#]

3.  Fare clic su **Accetto** per accettare i termini di licenza di 10gen, Inc.

4.  Dopo avere installato il driver fare clic su **Chiudi**.
    ![MongoDB C# Driver Installed][MongoDB C# Driver Installed]

Il driver MongoDB C# è ora installato. I riferimenti alle librerie **MongoDB.Driver.dll** e **MongoDB.Bson.dll** sono stati aggiunti al progetto.

![Riferimenti al driver MongoDB C#][Riferimenti al driver MongoDB C#]

### Aggiunta di un modello

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

### Aggiunta di un livello di accesso ai dati

In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto *MyTaskListApp*, quindi scegliere **Aggiungi**, **Nuova cartella** e scegliere la cartella denominata *DAL*. Fare clic con il pulsante destro del mouse sulla cartella *DAL* e scegliere **Aggiungi**, **Classe** per aggiungere una nuova classe. Assegnare al file della classe il nome *Dal.cs*. In *Dal.cs* sostituire il codice esistente con il codice seguente:

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

### Aggiunta di un controller

In **Esplora soluzioni** aprire il file *Controllers\\HomeController.cs* e sostituirne il contenuto con quello riportato di seguito:

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

### Impostazione dello stile del sito

Per modificare il titolo nella parte superiore della pagina, in **Esplora soluzioni** aprire il file Views\\Shared\\\_Layout.cshtml e sostituire "Application name" nell'intestazione della barra di navigazione con "My Task List Application":

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

Per aggiungere la possibilità di creare una nuova attività, fare clic con il pulsante destro del mouse sulla cartella *Views\\Home\\*, quindi scegliere **Aggiungi**, **Visualizzazione** per aggiungere una nuova visualizzazione. Assegnare alla visualizzazione il nome *Crea*. Sostituire il codice con il codice seguente:

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

![Solution Explorer][Solution Explorer]

### Impostazione della stringa di connessione MongoDB

In **Esplora soluzioni** aprire il file *DAL/Dal.cs*. Individuare la riga di codice seguente:

    private string connectionString = "mongodb://<vm-dns-name>";

Sostituire `<vm-dns-name> con il nome DNS della macchina virtuale che esegue MongoDB creata nel passaggio `[Creare una macchina virtuale e installare MongoDB][Creare una macchina virtuale e installare MongoDB] di questa esercitazione. Per individuare il nome DNS della macchina virtuale, passare al portale di gestione di Azure, selezionare **Macchine virtuali** e individuare **Nome DN**.

Se il nome DNS della macchina virtuale è "testlinuxvm.cloudapp.net" e MongoDB è in ascolto sulla porta predefinita 27017, la riga di codice della stringa di connessione avrà il seguente aspetto:

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Se l'endpoint della macchina virtuale specifica una porta esterna diversa per MongoDB, è possibile specificare la porta nella stringa di connessione:

    private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Per altre informazioni sulle stringhe di connessione di MongoDB, vedere l'argomento relativo alle [connessioni][connessioni].

### Test della distribuzione locale

Per eseguire l'applicazione sul computer di sviluppo, selezionare **Avvia debug** dal menu **Debug** oppure premere **F5**. IIS Express viene avviato e nel browser viene avviata la home page dell'applicazione. È possibile aggiungere una nuova attività, che verrà aggiunta al database MongoDB in esecuzione sulla macchina virtuale in Azure.

![Applicazione My Task List][Applicazione My Task List]

## <span class="short-header">Distribuire l'applicazione su un sito Web di Azure</span>Distribuire l'applicazione ASP.NET su un sito Web di Azure

In questa sezione verrà creato un sito Web e verrà distribuita l'applicazione ASP.NET My Task List usando Git.

<span id="createwebsite"></span></a>

### Creare un sito Web di Azure

In questa sezione verrà creato un sito Web di Azure.

1.  Aprire un Web browser e passare al [portale di gestione di Azure][portale di gestione di Azure]. Accedere con l'account Azure.
2.  Fare clic su **+Nuovo** nella parte inferiore della pagina, quindi selezionare **Sito Web** e infine **Creazione rapida**.
3.  Immettere un prefisso univoco per l'URL dell'applicazione.
4.  Scegliere un'area,
5.  Fare clic su **Crea sito Web**.

![Creazione di un nuovo sito Web][Creazione di un nuovo sito Web]

1.  Il sito Web verrà creato in modo rapido e visualizzato in **Siti Web**.

![WAWSDashboardMyTaskListApp][WAWSDashboardMyTaskListApp]

<span id="deployapp"></span></a>

### Distribuire l'applicazione ASP.NET nel sito Web usando Git

In questa sezione verrà distribuita l'applicazione ASP.NET My Task List usando Git.

1.  Fare clic sul nome del sito Web in **Siti Web**, quindi selezionare **Dashboard**. A destra, sotto riepilogo rapido, fare clic su **Imposta distribuzione dal controllo del codice sorgente**.
2.  Nella pagina **Dove è il codice sorgente?** scegliere **Repository Git locale**, quindi fare clic sulla freccia **Avanti**.
3.  Il repository Git verrà creato rapidamente. Prendere nota delle istruzioni riportate nella pagina visualizzata, in quanto verranno utilizzate nella sezione successiva.

    ![Il repository Git è pronto][Il repository Git è pronto]

4.  In **Esegui push dei file locali in Windows Azure** sono disponibili istruzioni per il push del codice in Azure. Le istruzioni avranno un aspetto simile al seguente:

    ![Push di file locali in Azure][Push di file locali in Azure]

5.  Se Git non è ancora installato, installarlo usando il link **Scaricalo qui** riportato al passaggio 1.
6.  Seguendo le istruzioni riportate al passaggio 2, eseguire il commit dei file locali.
7.  Aggiungere il repository Azure remoto ed eseguire il push dei file nel sito Web di Azure seguendo le istruzioni riportate nel passaggio 3.
8.  Al termine della distribuzione verrà visualizzata la seguente conferma:

    ![Distribuzione completata][Distribuzione completata]

9.  Il sito Web di Azure è ora disponibile. Nella pagina **Dashboard** controllare la presenza del sito e il campo **URL sito** per trovare l'URL del sito. Se sono state seguite le procedure di questa esercitazione, il sito dovrebbe essere disponibile all'URL: <http://mytasklistapp.azurewebsites.net>.

## Riepilogo

L'applicazione ASP.NET è stata correttamente distribuita in un sito Web di Azure. Per visualizzare il sito, fare clic sul link riportato nel campo **URL sito** della pagina **Dashboard**. Per altre informazioni sull'utilizzo di MongoDB per lo sviluppo di applicazioni C#, vedere il [centro linguistico CSharp][centro linguistico CSharp] di MongoDB.


  [Distribuire l'applicazione ASP.NET nel sito Web usando Git]: #deployapp
  [centro linguistico CSharp]: http://docs.mongodb.org/ecosystem/drivers/csharp/
  [sito Web ASP.net]: http://www.asp.net/
  [sito Web MVC ASP.NET]: http://www.asp.net/mvc
  [Azure]: http://www.windowsazure.com
  [Installazione di MongoDB in una macchina virtuale che esegue Windows Server in Azure]: /it-it/manage/windows/common-tasks/install-mongodb/
  [Installazione di MongoDB in una macchina virtuale che esegue CentOS Linux in Azure]: /it-it/manage/linux/common-tasks/mongodb-on-a-linux-vm/
  [Visual Studio Express 2013 per il Web]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
  [Visual Studio 2013]: http://www.microsoft.com/visualstudio/eng/2013-downloads
  [Pagina iniziale Nuovo progetto]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
  [Finestra di dialogo Nuovo progetto]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
  [Selezione di un template MVC]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
  [Applicazione ASP.NET MVC predefinita]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
  [Manage NuGet Packages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
  [Ricerca del driver MongoDB C#]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
  [MongoDB C# Driver Installed]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
  [Riferimenti al driver MongoDB C#]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
  [Solution Explorer]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
  [connessioni]: http://www.mongodb.org/display/DOCS/Connections
  [Applicazione My Task List]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Creazione di un nuovo sito Web]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
  [WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
  [Il repository Git è pronto]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
  [Push di file locali in Azure]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
  [Distribuzione completata]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png
