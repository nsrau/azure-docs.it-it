<properties 
	pageTitle="Creare un'app Web .NET in Azure con MongoDB mediante il componente aggiuntivo MongoLab" 
	description="Informazioni su come creare un'app Web .NET in Servizio app di Azure che archivia i dati in un database MongoDB ospitato da MongoLab." 
	tags="azure-classic-portal"
	services="app-service\web" 
	documentationCenter=".net" 
	authors="chrisckchang" 
	manager="partners@mongolab.com" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="chris@mongolab.com"/>



# Creare un'app Web .NET in Azure con MongoDB mediante il componente aggiuntivo MongoLab

  	<!-- The MongoLab workflow is not yet supported in the Preview Portal -->

<p><em>Autore: Eric Sedor, MongoLab</em></p>

Salve, benvenuti nel database MongoDB distribuito come servizio. In questa esercitazione si apprenderà come:

1. [Effettuare il provisioning del database][provision] - Il componente aggiuntivo [MongoLab](http://mongolab.com) di Azure Marketplace fornisce un database MongoDB ospitato nel cloud di Azure e gestito dalla piattaforma database cloud di MongoLab.
1. [Creare l'app][create] - Sarà una semplice applicazione C# ASP.NET MVC per prendere note.
1. [Distribuire l'app][deploy] - Collegando alcuni hook di configurazione, il push del codice all'[App Web di Servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) diventerà davvero semplice.
1. [Gestire il database][manage] - Verrà infine illustrato il portale di gestione del database basato sul Web di MongoLab, in cui è possibile cercare, visualizzare e modificare i dati con facilità.

In qualsiasi momento nel corso di questa esercitazione, sarà possibile inviare un messaggio di posta elettronica con eventuali domande a [support@mongolab.com](mailto:support@mongolab.com).

## Avvio rapido
Se si dispone già di un'app Web in Servizio app di Azure che si desidera usare o si conosce già Azure Marketplace, usare questa sezione per iniziare rapidamente. In caso contrario, passare alla sezione [Provisioning del database][provision] più avanti.
 
1. Aprire Azure Marketplace facendo clic su **Nuovo** > **Markeplace**.  
	<!-- ![Store][button-store] -->

1. Acquistare il componente aggiuntivo MongoLab.  
	![MongoLab][entry-mongolab]

1. Fare clic sul componente aggiuntivo MongoLab nell'elenco dei componenti aggiuntivi e fare clic su **Informazioni di connessione**.  
	![ConnectionInfoButton][button-connectioninfo]

1. Copiare MONGOLAB_URI negli Appunti.  
	![ConnectionInfoScreen][screen-connectioninfo] 
	**Questo URI contiene il nome utente e la password per il database. Trattare queste informazioni come dati sensibili e non condividerle.**

1. Aggiungere il valore all'elenco Stringhe di connessione nel menu Configurazione dell'applicazione Web di Azure:  
	![WebSiteConnectionStrings][focus-website-connectinfo]

1. In **Nome** digitare MONGOLAB_URI.

1. In **Valore** incollare la stringa di connessione ottenuta nella sezione precedente.

1. Selezionare **Personalizzato** nell'elenco a discesa Tipo (anziché la voce predefinita **SQLAzure**).

1. In Visual Studio installare il driver Mongo C# selezionando **Strumenti > Gestione pacchetti libreria > Console di Gestione pacchetti**. Nella console di gestione dei pacchetti, digitare **Install-Package mongocsharpdriver** e premere **INVIO**.

1. Configurare un hook nel codice per ottenere l'URI di connessione MongoLab da una variabile di ambiente:

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);

> **Nota:** Azure aggiunge il prefisso **CUSTOMCONNSTR_** alla stringa di connessione dichiarata in origine. Per questo motivo il codice fa riferimento a **CUSTOMCONNSTR_MONGOLAB_URI.** anziché a **MONGOLAB_URI**.

Si passerà ora all'esercitazione completa.

<a name="provision"></a>
## Provisioning del database

[AZURE.INCLUDE [howto-provision-mongolab](../../includes/howto-provision-mongolab.md)]

<a name="create"></a>
## Creare l'app

In questa sezione verrà trattata la creazione di un progetto C# ASP.NET di Visual Studio e illustrato l'utilizzo del driver C# MongoDB per creare una semplice app per prendere appunti. L'obiettivo è poter visitare l'app Web, scrivere una nota e visualizzare tutte le note lasciate in precedenza.

L'attività di sviluppo verrà eseguita in Visual Studio Express 2013 per il Web.

### Creare il progetto
Per iniziare, verrà utilizzato un modello di Visual Studio per questa app di esempio. Assicurarsi di usare .NET Framework 4.5.

1. Selezionare **File > nuovo progetto**. Viene visualizzata la finestra di dialogo Nuovo progetto:    
	![NewProject][dialog-mongolab-csharp-newproject]

1. Selezionare **Installati > Modelli > Visual C# > Web**.

1. Selezionare **.NET Framework 4.5** dal menu a discesa Versione .NET.

1. Scegliere **Applicazione MVC**.

1. Digitare _mongoNotes_ come **Nome progetto**. Se si sceglie un nome diverso, sarà necessario modificare il codice fornito in tutto il presente tutorial.

1. Scegliere **Strumenti > Gestione pacchetti libreria > Console di Gestione pacchetti**. Nella console di gestione dei pacchetti, digitare **Install-Package mongocsharpdriver** e premere **INVIO**.  
	![PMConsole][focus-mongolab-csharp-pmconsole] 
	Il driver MongoDB C# è integrato con il progetto e la riga seguente viene aggiunta automaticamente al file _packages.config_:

        < package id="mongocsharpdriver" version="1.9.2" targetFramework="net45" / >

### Aggiungere un modello di nota
In primo luogo, stabilire un modello per le note, semplicemente con una data e un contenuto di testo.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse su **Modelli** e scegliere **Aggiungi > Classe**. Denominare la nuova classe *Note.cs*.

1. Sostituire il codice generato automaticamente per questa classe con il seguente:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using MongoDB.Bson.Serialization.Attributes;
        using MongoDB.Bson.Serialization.IdGenerators;
        using MongoDB.Bson;
                
        namespace mongoNotes.Models
        {
            public class Note
            {
                public Note()
                {
                    Date = DateTime.UtcNow;
                }
                
                private DateTime date;
        
                [BsonId(IdGenerator = typeof(CombGuidGenerator))]
                public Guid Id { get; set; }
        
                [BsonElement("Note")]
                public string Text { get; set; }
        
                [BsonElement("Date")]
                public DateTime Date {
                    get { return date.ToLocalTime(); }
                    set { date = value;}
                }
            }
        }

### Aggiungere un livello di accesso ai dati
È importante stabilire un mezzo di accesso a MongoDB per recuperare e salvare le note. Il livello di accesso ai dati farà uso del modello di nota e verrà integrato in HomeController più avanti.

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto **mongoNotes** e selezionare **Aggiungi > Nuova cartella**. Denominare la cartella **DAL**.

1. Fare clic con il pulsante destro del mouse su **DAL** in Esplora soluzioni e selezionare **Aggiungi > Classe**. Denominare la nuova classe *Dal.cs*.

1. Sostituire il codice generato automaticamente per questa classe con il seguente:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using mongoNotes.Models;
        using MongoDB.Driver;
        using System.Configuration;

        namespace mongoNotes
        {
            public class Dal : IDisposable
            {
                private MongoServer mongoServer = null;
                private bool disposed = false;
        
                private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
                MongoUrl url;
        
                private string dbName = "myMongoApp";
                private string collectionName = "Notes";
        
                // Default constructor.        
                public Dal()
                {
                    url = new MongoUrl(connectionString);
                }
           
                public List<Note> GetAllNotes()
                {
                    try
                    {
                        MongoCollection<Note> collection = GetNotesCollection();
                        return collection.FindAll().ToList<Note>();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<Note>();
                    }
                }
        
                // Creates a Note and inserts it into the collection in MongoDB.
                public void CreateNote(Note note)
                {
                    MongoCollection<Note> collection = getNotesCollectionForEdit();
                    try
                    {
                        collection.Insert(note);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
        
                private MongoCollection<Note> GetNotesCollection()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
                    return noteCollection;
                }
        
                private MongoCollection<Note> getNotesCollectionForEdit()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> notesCollection = database.GetCollection<Note>(collectionName);
                    return notesCollection;
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

1. Notare il frammento seguente nel codice qui sopra:
            
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  
Qui si accedere a una variabile di ambiente che è possibile configurare in seguito. Se un'istanza locale di mongo è in esecuzione a scopo di sviluppo, potrebbe essere necessario impostare temporaneamente questo valore su "localhost".
  
  Impostare anche il nome del database. In particolare, impostare il valore **dbName** sul nome immesso quando si è eseguito il provisioning del componente aggiuntivo MongoLab.

1. Esaminare infine il codice seguente in **GetNotesCollection()**:  

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
  Non vi sono modifiche da apportare qui. È sufficiente sapere che questo è il metodo tramite cui si ottiene un oggetto MongoCollection per l'esecuzione di inserimenti, aggiornamenti e query, come illustrato di seguito per **GetAllNotes()**:

        collection.FindAll().ToList<Note>();

Per altre informazioni sull'uso del driver C# MongoDB, vedere la [guida introduttiva al driver CSharp](http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "Guida introduttiva al driver CSharp") sul sito mongodb.org.

### Aggiungere una visualizzazione di creazione
A questo punto è necessario aggiungere una visualizzazione per la creazione di una nuova nota.

1. Fare clic con il pulsante destro del mouse sulla voce **Visualizzazioni > Home** in Esplora soluzioni, quindi selezionare **Aggiungi > Visualizzazione**. Denominare la nuova visualizzazione **Create** e fare clic su **Aggiungi**.

1. Sostituire il codice generato automaticamente per questa visualizzazione (**Create.cshtml**) con il seguente:

        @model mongoNotes.Models.Note
        
        <script src="@Url.Content("~/Scripts/jquery-1.5.1.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
        
        @using (Html.BeginForm("Create", "Home")) {
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>New Note</legend>
                <h3>New Note</h3>       
                <div class="editor-label">
                    @Html.LabelFor(model => model.Text)
                </div>
                <div class="editor-field">
                    @Html.EditorFor(model => model.Text)
                </div>
               <p>
                    <input type="submit" value="Create" />
               </p>
           </fieldset>
        }

### Modificare index.cshtml
A questo punto, aggiungere un semplice layout per la visualizzazione e la creazione delle note nell'app Web.

1. In **Visualizzazioni > Home** aprire **Index.cshtml** e sostituirne il contenuto con quello seguente:  

        @model IEnumerable<mongoNotes.Models.Note>
        
        @{
            ViewBag.Title = "Notes";
        }
        
        <h2>My Notes</h2>

        <table border="1">
            <tr>
                <th>Date</th>
                <th>Note Text</th>      
            </tr>
        
        @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Date)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Text)
                </td>       
            </tr>
        }
        
        </table>
        <div>  @Html.Partial("Create", new mongoNotes.Models.Note())</div>

### Modificare HomeController.cs
Infine, HomeController deve essere messo in grado di creare un'istanza per il livello di accesso ai dati e di applicarla alle visualizzazioni.

1. In Esplora soluzioni aprire **HomeController.cs** da **Controller** e sostituirne il contenuto con quello riportato di seguito:  

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using System.Web.Mvc;
        using mongoNotes.Models;
        using System.Configuration;
        
        namespace mongoNotes.Controllers
        {
            public class HomeController : Controller, IDisposable
            {
                private Dal dal = new Dal();
                private bool disposed = false;
                //
                // GET: /Task/
        
                public ActionResult Index()
                {
                    return View(dal.GetAllNotes());
                }
        
                //
                // GET: /Task/Create
        
                public ActionResult Create()
                {
                    return View();
                }
        
                //
                // POST: /Task/Create
        
                [HttpPost]
                public ActionResult Create(Note note)
                {
                    try
                    {
                        dal.CreateNote(note);
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
    
<a name="deploy"></a>
## Distribuire l'app

Ora che l'applicazione è stata sviluppata, è possibile creare un'app Web in Servizio app di Azure per ospitarla, configurarla e distribuire il codice. L'argomento principale di questa sezione è l'utilizzo della stringa di connessione MongoDB (URI). Si configurerà una variabile di ambiente nell'app Web con questo URI per mantenere l'URI separato dal codice. È consigliabile considerare l'URI come un'informazione riservata in quanto contiene le credenziali per connettersi al database.

### Creare una nuova app Web e ottenere il file di impostazioni di pubblicazione
La creazione di un'app Web in Servizio app di Azure è molto facile, in particolare grazie al fatto che Azure genera automaticamente un profilo di pubblicazione per Visual Studio.

1. Nel portale di Azure fare clic su **Nuovo**. 
	![Nuovo][button-new]

1. Selezionare **Calcolo > App Web > Creazione rapida**.
	<!-- ![CreateWebApp][screen-mongolab-newwebsite] -->

1. Immettere un prefisso URL. Scegliere il nome desiderato ma tenere presente che deve essere univoco. "mongoNotes" non sarà probabilmente disponibile.

1. Fare clic su **Crea app Web**.

1. Al termine della creazione dell'app Web, fare clic sul nome dell'app Web nell'elenco delle app Web. Viene visualizzato il dashboard dell'app Web. 
	![WebAppDashboard][screen-mongolab-websitedashboard]

1. Fare clic su **Scarica profilo di pubblicazione** in **riepilogo rapido** e quindi salvare il file .PublishSettings in una directory di propria scelta.
![DownloadPublishProfile][button-website-downloadpublishprofile]

In alternativa, è anche possibile configurare l'app Web direttamente da Visual Studio. Quando si collega l'account Azure a Visual Studio, seguire le istruzioni per configurare un'app Web da tale applicazione. Al termine, è sufficiente fare clic con il pulsante destro del mouse sul nome del progetto in Esplora soluzioni per distribuirlo in Azure. Sarà comunque necessario configurare la stringa di connessione di MongoLab, come descritto in dettaglio nella procedura seguente.

### Ottenere la stringa di connessione MongoLab

[AZURE.INCLUDE [howto-get-connectioninfo-mongolab](../../includes/howto-get-connectioninfo-mongolab.md)]

### Aggiungere la stringa di connessione alle variabili di ambiente dell'app Web

[AZURE.INCLUDE [howto-save-connectioninfo-mongolab](../../includes/howto-save-connectioninfo-mongolab.md)]

### Pubblicare l'app Web
1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto **mongoNotes** in Esplora soluzioni, quindi selezionare **Pubblica**. Viene visualizzata la relativa finestra di dialogo:  
	<!-- ![Publish][dialog-mongolab-vspublish] -->

1. Fare clic su **Importa** e selezionare il file .PublishSettings dalla directory di download scelta in precedenza. Il file popola automaticamente i valori nella finestra di dialogo Pubblica.

1. Fare clic su **Convalida connessione** per eseguire il test del file.

1. Dopo l'esito positivo della convalida, fare clic su **Pubblica**. Dopo il completamento della pubblicazione, viene aperta una nuova scheda del browser e viene visualizzata l'app Web.

1. Immettere un testo per la nota, fare clic su **Crea** e osservare i risultati.
	![HelloMongoAzure][screen-mongolab-sampleapp]

<a name="manage"></a>
## Gestire il database

[AZURE.INCLUDE [howto-access-mongolab-ui](../../includes/howto-access-mongolab-ui.md)]

Congratulazioni. verrà avviata un'applicazione C# ASP.NET supportata da un database MongoDB ospitato da MongoLab. Ora che si dispone di un database MongoLab, è possibile contattare [support@mongolab.com](mailto:support@mongolab.com) per eventuali domande o problemi relativamente al database o per assistenza su MongoDB o sul driver C#. È ora possibile utilizzare l'applicazione.

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

[screen-mongolab-sampleapp]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-sampleapp.png
[dialog-mongolab-vspublish]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-vspublish.png
[button-website-downloadpublishprofile]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-website-downloadpublishprofile.png
[screen-mongolab-websitedashboard]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/screen-mongolab-newwebsite.png
[button-new]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-new.png
[dialog-mongolab-csharp-newproject]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-newproject.png
[dialog-mongolab-csharp-projecttemplate]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-projecttemplate.png
[focus-mongolab-csharp-pmconsole]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-csharp-pmconsole.png
[button-store]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-store.png
[entry-mongolab]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/entry-mongolab.png
[button-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/button-connectioninfo.png
[screen-connectioninfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage

 

<!------HONumber=July15_HO4-->