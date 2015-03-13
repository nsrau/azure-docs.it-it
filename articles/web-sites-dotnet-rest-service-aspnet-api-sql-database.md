<properties 
	pageTitle="Servizio REST .NET usando l'API Web - Esercitazione di Azure" 
	description="Un'esercitazione che illustra come distribuire un'app che usa l'API Web ASP.NET in un sito Web di Azure tramite Visual Studio." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="riande" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/06/2014" 
	ms.author="riande"/>

# Servizio REST usando l'API Web ASP.NET e Database SQL 

***Autori [Rick Anderson](https://twitter.com/RickAndMSFT) e Tom Dykstra.***

In questa esercitazione viene illustrato come distribuire un'applicazione Web ASP.NET in un sito Web di Azure usando la procedura guidata Pubblica sito Web in Visual Studio 2013 o Visual Studio Express 2013 per il Web. 

È possibile aprire gratuitamente un account Azure e, se non si dispone già di Visual Studio 2013, con l'SDK verrà installato automaticamente Visual Studio Express 2013 per il Web. Sarà quindi possibile iniziare a sviluppare per Azure del tutto gratuitamente.

In questa esercitazione si presuppone che l'utente non abbia mai usato Azure. Al termine dell'esercitazione, si disporrà di un'applicazione Web semplice in esecuzione nel cloud.
 
Si apprenderà come:

* Abilitare il sistema per lo sviluppo in Azure installando Azure SDK.
* Creare un progetto ASP.NET MVC 5 di Visual Studio sicuro e pubblicarlo in un sito Web di Azure.
* Creare l'API Web ASP.NET per consentire chiamate all'API RESTful.
* Usare un database SQL per l'archiviazione di dati in Azure.
* Pubblicare aggiornamenti dell'applicazione in Azure.

Verrà creata una semplice applicazione Web di elenco contatti basata su ASP.NET MVC 5 che utilizza ADO.NET Entity Framework per l'accesso al database. Nella figura seguente è illustrata l'applicazione completata:

![screenshot of web site][intro001]

Contenuto dell'esercitazione:

* [Configurare l'ambiente di sviluppo][setupdbenv]
* [Configurare l'ambiente Azure][setupwindowsazureenv]
* [Creare un'applicazione ASP.NET MVC 5][createapplication]
* [Distribuire l'applicazione in Azure][deployapp1]
* [Aggiungere un database all'applicazione][adddb]
* [Aggiungere un controller e una visualizzazione per i dati][addcontroller]
* [Aggiungere un'interfaccia dell'API Web RESTful][addwebapi]
* [Aggiungere la protezione XSRF][]
* [Pubblicare l'aggiornamento dell'applicazione in Azure e nel database SQL][deploy2]

<a name="bkmk_setupdevenv"></a>
<!-- the next line produces the "Set up the development environment" section as see at http://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/ -->
[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

<h2><a name="bkmk_setupwindowsazure"></a>Configurare l'ambiente Azure</h2>

Configurare l'ambiente Azure creando un sito Web di Azure e un database SQL.

### Creare un sito Web e un database SQL in Azure

Nel passaggio successivo verrà creato il sito Web di Azure e il database SQL che verranno usati dall'applicazione.

Il sito Web di Azure verrà eseguito in un ambiente di hosting condiviso, ovvero verrà eseguito su macchine virtuali condivise con altri client di Azure. Un ambiente di hosting condiviso è un punto di partenza economicamente conveniente per iniziare a usare il cloud. In seguito, in caso di incremento del traffico Web, è possibile scalare l'applicazione in modo da soddisfare le nuove esigenze tramite l'esecuzione su macchine virtuali dedicate. Se è necessaria un'architettura più complessa, è possibile eseguire la migrazione a un servizio cloud Azure. I servizi cloud vengono eseguiti su macchine virtuali dedicate, che possono essere configurate in base alle proprie esigenze specifiche.

Database SQL è un servizio di database relazionale basato sul cloud che si avvale delle tecnologie SQL Server. Gli strumenti e le applicazioni utilizzabili con SQL Server sono utilizzabili anche con database SQL.

1. Nel [portale di gestione di Azure](https://manage.windowsazure.com) fare clic su **Siti Web** nella scheda di sinistra, quindi su **Nuovo**.

2. Fare clic su **CREAZIONE PERSONALIZZATA**.

	![Collegamento di creazione con il database nel portale di gestione](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr6.PNG)

	Verrà aperta la procedura guidata **Nuovo sito Web - Creazione personalizzata**. 

3. Nel passaggio **Nuovo sito Web** della procedura guidata immettere nella casella **URL** una stringa da usare come URL univoco per l'applicazione. L'URL completo sarà costituito da quanto immesso in questa casella e dal suffisso visualizzato sotto la casella di testo. Nell'illustrazione è visualizzato "contactmgr11", ma poiché è probabile che questo URL sia già in uso, sarà necessario sceglierne un altro.

1. Nell'elenco a discesa **Area** scegliere l'area geografica più vicina.

1. Nell'elenco a discesa **Database** scegliere **Crea un database SQL 20 MB gratuito**.

	![Passaggio di creazione di un nuovo sito Web nella procedura guidata di creazione del nuovo sito Web con il database](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrCWS.png)

6. Fare clic sulla freccia a destra nella parte inferiore della finestra di dialogo.

	Verrà visualizzato il passaggio **Impostazioni database** della procedura guidata.

7. Nella casella **Nome** immettere  *ContactDB*.

8. Nella casella **Server** selezionare **Nuovo server di database SQL**. In alternativa, se è stato creato in precedenza un database SQL Server, è possibile selezionare tale database SQL Server dall'elenco a discesa.

9. Fare clic sulla freccia a destra nella parte inferiore della finestra di dialogo.

10. Immettere un **NOME DI ACCESSO** e una **PASSWORD** per l'amministratore. Se è stata selezionata l'opzione **Nuovo server di database SQL**, in questo passaggio non sarà necessario immettere un nome e una password esistenti, ma un nuovo nome e una nuova password, definiti ora e da usare in seguito per l'accesso al database. Se è stato selezionato un database SQL Server creato in precedenza, verrà richiesta la password del nome account SQL Server creato in precedenza. Per questa esercitazione non verrà selezionata la casella **Avanzate**. La casella **Avanzate** consente di impostare la dimensione del database (il valore predefinito è 1 GB, ma è possibile aumentarlo fino a 150 GB) e le regole di confronto.

11. Fare clic sul segno di spunta nella parte inferiore della finestra di dialogo per indicare che le operazioni sono state completate.

	![Passaggio delle impostazioni del database nella procedura guidata di creazione di un nuovo sito Web con il database][setup007]

	 Nell'immagine seguente viene mostrato l'uso di un database SQL Server e un accesso esistenti.
	
	![Passaggio delle impostazioni del database nella procedura guidata di creazione di un nuovo sito Web con il database][rxPrevDB]

	Il portale di gestione torna alla pagina Siti Web e nella colonna **Stato** viene indicato che la creazione del sito è in corso. In genere, dopo meno di un minuto nella colonna **Stato** viene indicato che il sito è stato creato correttamente. Il numero di siti disponibili nell'account viene visualizzato nella barra di spostamento a sinistra accanto all'icona **Siti Web** e il numero di database viene visualizzato accanto all'icona **Database SQL**.

<!-- [Websites page of Management Portal, website created][setup009] -->

<h2><a name="bkmk_createmvc4app"></a>Creare un'applicazione ASP.NET MVC 5</h2>

Il sito Web di Azure è stato creato, ma non include ancora alcun contenuto. Il passaggio successivo consiste nel creare il progetto dell'applicazione Web di Visual Studio che verrà pubblicato in Azure.

### Creare il progetto

1. Avviare Visual Studio 2013.
1. Scegliere **Nuovo progetto** dal menu **File**.
3. Nella finestra di dialogo **Nuovo progetto** espandere **Visual C#** e selezionare **Web**, quindi selezionare **Applicazione Web ASP.NET MVC 5**. Mantenere l'opzione **.NET Framework 4.5** predefinita. Assegnare all'applicazione il nome **ContactManager** e fare clic su **OK**.
	![Finestra di dialogo Nuovo progetto](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.PNG)]
1. Nella finestra di dialogo **Nuovo progetto ASP.NET** selezionare il modello **MVC**, selezionare **API Web**, quindi fare clic su **Modifica autenticazione**.

	![Finestra di dialogo del nuovo progetto ASP.NET](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG)

1. Nella finestra di dialogo **Modifica autenticazione** fare clic su **Nessuna autenticazione**, quindi fare clic su **OK**.

	![Nessuna autenticazione](./media/web-sites-dotnet-get-started-vs2013/GS13noauth.png)

	L'applicazione di esempio che si sta creando non includerà funzionalità che richiedono l'accesso agli utenti. Per informazioni su come implementare le funzionalità di autenticazione e autorizzazione, vedere la sezione [Passaggi successivi](#nextsteps) alla fine dell'esercitazione. 

1. Nella finestra di dialogo **Nuovo progetto ASP.NET** fare clic su **OK**.

	![Finestra di dialogo del nuovo progetto ASP.NET](./media/web-sites-dotnet-get-started-vs2013/GS13newaspnetprojdb.png)

### Impostare intestazione e piè di pagina


1. In **Esplora soluzioni** espandere la cartella  *Views\Shared* e aprire il file *_Layout.cshtml*.

	![_Layout.cshtml in Esplora soluzioni][newapp004]

1. Sostituire il contenuto del file *_Layout.cshtml* con il seguente codice.


		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="utf-8" />
		    <title>@ViewBag.Title - Contact Manager</title>
		    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
		    <meta name="viewport" content="width=device-width" />
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		</head>
		<body>
		    <header>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p class="site-title">@Html.ActionLink("Contact Manager", "Index", "Home")</p>
		            </div>
		        </div>
		    </header>
		    <div id="body">
		        @RenderSection("featured", required: false)
		        <section class="content-wrapper main-content clear-fix">
		            @RenderBody()
		        </section>
		    </div>
		    <footer>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p>&copy; @DateTime.Now.Year - Contact Manager</p>
		            </div>
		        </div>
		    </footer>
		    @Scripts.Render("~/bundles/jquery")
		    @RenderSection("scripts", required: false)
		</body>
		</html>
			
Il markup precedente cambia il nome dell'app da "My ASP.NET App" a "Contact Manager" e rimuove i collegamenti a **Home**, **About** e **Contact**.

### Eseguire l'applicazione in locale

1. Premere CTRL+F5 per eseguire l'applicazione.
La home page dell'applicazione verrà visualizzata nel browser predefinito.
	![Home page dell'elenco azioni](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr5.PNG)

Non è necessario eseguire per il momento altre operazioni per creare l'applicazione che verrà distribuita in Azure. La funzionalità di database verrà aggiunta in un secondo momento.

<h2><a name="bkmk_deploytowindowsazure1"></a>Distribuire l'applicazione in Azure</h2>

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** dal menu di scelta rapida.

	![Pubblica nel menu di scelta rapida del progetto][PublishVSSolution]

	Viene avviata la procedura guidata **Pubblica sito Web**.

2. Nella scheda **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Importa**.

	![Import publish settings][ImportPublishSettings]

	Verrà visualizzata la finestra di dialogo **Importa profilo di pubblicazione**.

 3.	Selezionare Import from an Azure Web Site. Se non lo si è già fatto, è innanzitutto necessario eseguire l'accesso. Fare clic su **Accedi**. Immettere l'utente associato alla sottoscrizione corrente e seguire le istruzioni visualizzate.

	![sign in](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr7.png)

	Selezionare il sito Web nell'elenco a discesa e fare clic su **OK**.

	![Import Publish Profile](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr8.png)

8. Nella scheda **Connessione** fare clic su **Convalida connessione** per verificare che le impostazioni siano corrette.

	![Validate connection][ValidateConnection]

9. Dopo la convalida della connessione, accanto al pulsante **Convalida connessione** verrà visualizzato un segno di spunta verde.

	![connection successful icon and Next button in Connection tab][firsdeploy007]

1. Fare clic su **Avanti**.

	![Settings tab](./media/web-sites-dotnet-get-started-vs2013/GS13SettingsTab.png)

	È possibile accettare le impostazioni predefinite in questa scheda.  Poiché verrà distribuita una configurazione della build di rilascio, non è necessario eliminare i file nel server di destinazione, precompilare l'applicazione o escludere i file della cartella App_Data. Se non si desidera eseguire il debug nel sito Azure attivo, sarà necessario distribuire una configurazione di debug (non di rilascio). Vedere la sezione [Passaggi successivi](#nextsteps) alla fine dell'esercitazione.

12. Nella scheda **Anteprima** fare clic su **Avvia anteprima**.

	Nella scheda viene visualizzato un elenco dei file che verranno copiati nel server. La visualizzazione dell'anteprima non è necessaria per pubblicare l'applicazione, ma è una funzione utile da conoscere. In questo caso, non è richiesto alcun intervento da parte dell'utente sull'elenco di file visualizzato. Alla pubblicazione successiva, nell'elenco di anteprima saranno visualizzati solo i file modificati nel frattempo.

	![StartPreview button in the Preview tab][firsdeploy009]

12. Fare clic su **Pubblica**.

	In Visual Studio verrà avviato il processo di copia dei file nel server Azure. Nella finestra **Output** vengono indicate le azioni di distribuzione effettuate e viene segnalato il corretto completamento della distribuzione.

14. Nel browser predefinito verrà automaticamente aperto l'URL del sito distribuito.

	L'applicazione creata verrà ora eseguita nel cloud.
	
	![To Do List home page running in Azure][rxz2]

<h2><a name="bkmk_addadatabase"></a>Aggiungere un database all'applicazione</h2>

Si passerà quindi all'aggiornamento dell'applicazione MVC, in modo da aggiungere la possibilità di visualizzare e aggiornare i contatti e di archiviare i dati in un database. L'applicazione utilizzerà Entity Framework per creare il database e per leggere e aggiornare i dati nel database.

### Aggiungere classi del modello di dati per i contatti

Creare innanzitutto un semplice modello di dati nel codice.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella Modelli, quindi scegliere **Aggiungi** e infine **Classe**.

	![Add Class in Models folder context menu][adddb001]

2. Nella finestra di dialogo **Aggiungi nuovo elemento** assegnare al nuovo file di classe il nome  *Contact.cs*, quindi fare clic su **Aggiungi**.

	![Add New Item dialog box][adddb002]

3. Sostituire il contenuto del file Contacts.cs con il seguente codice.

		using System.Globalization;
		namespace ContactManager.Models
		{
    		public class Contact
   			{
        		public int ContactId { get; set; }
				public string Name { get; set; }
				public string Address { get; set; }
	        	public string City { get; set; }
				public string State { get; set; }
				public string Zip { get; set; }
				public string Email { get; set; }
				public string Twitter { get; set; }
				public string Self
        		{
            		get { return string.Format(CultureInfo.CurrentCulture,
				         "api/contacts/{0}", this.ContactId); }
            		set { }
        		}
    		}
		}

La classe **Contacts** consente di definire i dati che verranno archiviati per ogni contatto, oltre a una chiave primaria, ContactID, necessaria per il database. È possibile ottenere altre informazioni sui modelli di dati nella sezione [Passaggi successivi](#nextsteps) alla fine dell'esercitazione.

### Creare pagine Web che consentono agli utenti dell'app di usare i contatti

La caratteristica scaffolding di ASP.NET MVC consente di generare automaticamente codice per l'esecuzione di azioni di creazione, lettura, aggiornamento ed eliminazione (CRUD, Create, Read, Update, Delete).

<h2><a name="bkmk_addcontroller"></a>Aggiungere un controller e una visualizzazione per i dati</h2>

1. In **Esplora soluzioni** espandere la cartella Controllers.

3. Creare il progetto **(CTRL+MAIUSC+B)**. Per usare il meccanismo scaffolding, è innanzitutto necessario creare il progetto. 

4. Fare clic con il pulsante destro del mouse sulla cartella Controllers, quindi scegliere **Aggiungi** e infine **Controller**.

	![Add Controller in Controllers folder context menu][addcode001]

1. Nella finestra di dialogo **Add Scaffold** selezionare **MVC Controller with views, using Entity Framework**, quindi fare clic su **Aggiungi**.

 ![Add controller](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.PNG)

6. Impostare il nome del controller su **HomeController**. Selezionare **Contact** come classe del modello. Fare clic sul pulsante **Nuovo contesto dati** e accettare il contesto predefinito "ContactManager.Models.ContactManagerContext" per **Nuovo tipo di contesto dati**. Fare clic su **Aggiungi**.

	![Add Controller dialog box](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr9.PNG)

	Verrà visualizzata una finestra di dialogo con il messaggio: "Un file con nome HomeController esiste già. Sostituirlo?" Fare clic su **Sì**. Il file HomeController creato con il nuovo progetto verrà sovrascritto. Per l'elenco dei contatti, verrà usato il nuovo file HomeController.

	In Visual Studio verranno creati metodi e visualizzazioni di un controller per operazioni CRUD di database per oggetti **Contact**.

## Abilitazione delle migrazioni, creazione del database, aggiunta di dati di esempio e di un inizializzatore di dati ##

L'attività successiva consiste nell'abilitare la funzionalità [Migrazioni Code First](http://curah.microsoft.com/55220), in modo da creare il database basato sul modello di dati creato.

1. Scegliere **Gestione pacchetti libreria** dal menu **Strumenti**, quindi fare clic su **Console di Gestione pacchetti**.

	![Package Manager Console in Tools menu][addcode008]

2. Nella finestra **Console di Gestione pacchetti** immettere il seguente comando:

		enable-migrations 
  
	Il comando **enable-migrations** consente di creare una cartella  *Migrations* e di inserire in tale cartella un file  *Configuration.cs*, che può essere modificato per configurare le migrazioni. 

2. Nella finestra **Console di Gestione pacchetti** immettere il seguente comando:

		add-migration Initial

	Il comando **add-migration Initial** consente di generare una classe denominata **&lt;<indicatore_data>&gt;Initial** che crea il database. Il primo parametro ( *Initial*) è arbitrario e viene usato per creare il nome del file. È possibile visualizzare i nuovi file di classe in **Esplora soluzioni**.

	Nella classe **Initial** il metodo **Up** consente di creare la cartella Contacts e il metodo **Down**, usato quando si vuole tornare allo stato precedente, consente di rimuoverla.

3. Aprire il file  *Migrations\Configuration.cs*. 

4. Aggiungere gli spazi dei nomi seguenti. 

    	 using ContactManager.Models;

5. Sostituire il metodo  *Seed* con il seguente codice:
		
        protected override void Seed(ContactManager.Models.ContactManagerContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
                   Twitter = "debra_example"
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                    Twitter = "thorsten_example"
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                    Twitter = "yuhong_example"
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                    Twitter = "jon_example"
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                    Twitter = "diliana_example"
                }
                );
        }

	Il codice precedente consente di inizializzare il database con le informazioni sui contatti. Per altre informazioni sul seeding del database, vedere la pagina relativa al [debug di database di Entity Framework (EF)](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


1. In **Console di Gestione pacchetti** immettere il comando seguente:

		update-database

	![Package Manager Console commands][addcode009]

	Il comando **update-database** consente di eseguire la prima migrazione al fine di creare il database. Per impostazione predefinita, il database viene creato come database LocalDB di SQL Server Express.

1. Premere CTRL+F5 per eseguire l'applicazione. 

Nell'applicazione vengono mostrati i dati di seeding e sono disponibili collegamenti per la modifica, i dettagli e l'eliminazione.

![MVC view of data][rxz3]

<h2><a name="bkmk_addview"></a>Modificare la visualizzazione</h2>

1. Aprire il file  *Views\Home\Index.cshtml*. Nel passaggio successivo il markup generato verrà sostituito con codice che usa [jQuery](http://jquery.com/) e [Knockout.js](http://knockoutjs.com/). Questo nuovo codice recupera l'elenco dei contatti con l'API Web e JSON e quindi associa i dati dei contatti all'interfaccia utente usando knockout.js. Per altre informazioni, vedere la sezione [Passaggi successivi](#nextsteps) alla fine dell'esercitazione. 


2. Sostituire il contenuto del file con il seguente codice.

		@model IEnumerable<ContactManager.Models.Contact>
		@{
		    ViewBag.Title = "Home";
		}
		@section Scripts {
		    @Scripts.Render("~/bundles/knockout")
		    <script type="text/javascript">
		        function ContactsViewModel() {
		            var self = this;
		            self.contacts = ko.observableArray([]);
		            self.addContact = function () {
		                $.post("api/contacts",
		                    $("#addContact").serialize(),
		                    function (value) {
		                        self.contacts.push(value);
		                    },
		                    "json");
		            }
		            self.removeContact = function (contact) {
		                $.ajax({
		                    type: "DELETE",
		                    url: contact.Self,
		                    success: function () {
		                        self.contacts.remove(contact);
		                    }
		                });
		            }

		            $.getJSON("api/contacts", function (data) {
		                self.contacts(data);
		            });
		        }
		        ko.applyBindings(new ContactsViewModel());	
		</script>
		}
		<ul id="contacts" data-bind="foreach: contacts">
		    <li class="ui-widget-content ui-corner-all">
		        <h1 data-bind="text: Name" class="ui-widget-header"></h1>
		        <div><span data-bind="text: $data.Address || 'Address?'"></span></div>
		        <div>
		            <span data-bind="text: $data.City || 'City?'"></span>,
		            <span data-bind="text: $data.State || 'State?'"></span>
		            <span data-bind="text: $data.Zip || 'Zip?'"></span>
		        </div>
		        <div data-bind="if: $data.Email"><a data-bind="attr: { href: 'mailto:' + Email }, text: Email"></a></div>
		        <div data-bind="ifnot: $data.Email"><span>Email?</span></div>
		        <div data-bind="if: $data.Twitter"><a data-bind="attr: { href: 'http://twitter.com/' + Twitter }, text: '@@' + Twitter"></a></div>
		        <div data-bind="ifnot: $data.Twitter"><span>Twitter?</span></div>
		        <p><a data-bind="attr: { href: Self }, click: $root.removeContact" class="removeContact ui-state-default ui-corner-all">Remove</a></p>
		    </li>
		</ul>
		<form id="addContact" data-bind="submit: addContact">
		    <fieldset>
		        <legend>Add New Contact</legend>
		        <ol>
		            <li>
		                <label for="Name">Name</label>
		                <input type="text" name="Name" />
		            </li>
		            <li>
		                <label for="Address">Address</label>
		                <input type="text" name="Address" >
		            </li>
		            <li>
		                <label for="City">City</label>
		                <input type="text" name="City" />
		            </li>
		            <li>
		                <label for="State">State</label>
		                <input type="text" name="State" />
		            </li>
		            <li>
		                <label for="Zip">Zip</label>
		                <input type="text" name="Zip" />
		            </li>
		            <li>
		                <label for="Email">E-mail</label>
		                <input type="text" name="Email" />
		            </li>
		            <li>
		                <label for="Twitter">Twitter</label>
		                <input type="text" name="Twitter" />
		            </li>
		        </ol>
		        <input type="submit" value="Add" />
		    </fieldset>
		</form>

3. Fare clic con il pulsante destro del mouse sulla cartella Content, scegliere **Aggiungi**, quindi fare clic su **Nuovo elemento**.

	![Add style sheet in Content folder context menu][addcode005]

4. Nella finestra di dialogo **Aggiungi nuovo elemento** immettere **Stile** nella casella di ricerca in alto a destra e quindi selezionare **Foglio di stile**.
	![Add New Item dialog box][rxStyle]

5. Assegnare al file il nome  *Contacts.css* e fare clic su **Aggiungi**. Sostituire il contenuto del file con il seguente codice.
    
        .column {
            float: left;
            width: 50%;
            padding: 0;
            margin: 5px 0;
        }
        form ol {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }
        form li {
            padding: 1px;
            margin: 3px;
        }
        form input[type="text"] {
            width: 100%;
        }
        #addContact {
            width: 300px;
            float: left;
            width:30%;
        }
        #contacts {
            list-style-type: none;
            margin: 0;
            padding: 0;
            float:left;
            width: 70%;
        }
        #contacts li {
            margin: 3px 3px 3px 0;
            padding: 1px;
            float: left;
            width: 300px;
            text-align: center;
            background-image: none;
            background-color: #F5F5F5;
        }
        #contacts li h1
        {
            padding: 0;
            margin: 0;
            background-image: none;
            background-color: Orange;
            color: White;
            font-family: Trebuchet MS, Tahoma, Verdana, Arial, sans-serif;
        }
        .removeContact, .viewImage
        {
            padding: 3px;
            text-decoration: none;
        }

	Questo foglio di stile verrà usato per il layout, i colori e gli stili nell'app Contact Manager.

6. Aprire il file  *App_Start\BundleConfig.cs*.


7. Aggiungere il seguente codice per registrare il plug-in [Knockout](http://knockoutjs.com/index.html "KO").

		bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
		            "~/Scripts/knockout-{version}.js"));
	In questo esempio viene usato il plug-in Knockout per semplificare il codice JavaScript dinamico che gestisce i modelli delle schermate.

8. Modificare la voce contents/css per registrare il foglio di stile  *contacts.css*. Sostituire la riga seguente:

                 bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/site.css"));
Con:

        bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/contacts.css",
                   "~/Content/site.css"));

1. Nella finestra Console di Gestione pacchetti eseguire il comando seguente per installare Knockout.

	Install-Package knockoutjs

<h2><a name="bkmk_addwebapi"></a>Aggiungere un controller per l'interfaccia dell'API Web RESTful</h2>

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sulla cartella Controllers, quindi scegliere **Aggiungi** e infine **Controller**. 

1. Nella finestra di dialogo **Aggiungi scaffold** immettere **Controller Web API 2 con azioni che usano Entity Framework**, quindi fare clic su **Aggiungi**.

	![Add API controller](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt1.PNG)

4. Nella finestra di dialogo **Aggiungi controller** immettere "ContactsController" come nome del controller. In **Classe modello** selezionare "Contact (ContactManager.Models)".  Mantenere il valore predefinito per **Classe contesto di dati**. 

6. Fare clic su **Aggiungi**.

### Eseguire l'applicazione in locale

1. Premere CTRL+F5 per eseguire l'applicazione.

	![Index page][intro001]

2. Immettere un contatto e fare clic su **Aggiungi**. L'app torna alla home page e visualizza il contatto immesso.

	![Index page with to-do list items][addwebapi004]

3. Nel browser aggiungere **/api/contacts** all'URL.

	L'URL risultante sarà simile a http://localhost:1234/api/contacts. L'API Web RESTful aggiunta restituirà i contatti archiviati. In Firefox e Chrome i dati saranno visualizzati in formato XML.

	![Index page with to-do list items][rxFFchrome]
	

	In Internet Explorer verrà visualizzato un messaggio in cui viene chiesto se si desidera aprire o salvare i contatti.

	![Web API save dialog][addwebapi006]
	
	
	È possibile aprire i contatti restituiti in Blocco note o in un browser.
	
	Questo output può essere usato da altre applicazioni, ad esempio una pagina Web o un'applicazione per dispositivi mobili.

	![Web API save dialog][addwebapi007]

	**Avviso di sicurezza**: a questo punto, l'applicazione non è sicura ed è vulnerabile agli attacchi di richiesta intersito falsa (Cross-Site Request Forgery, CSRF). Questa vulnerabilità verrà rimossa più avanti nell'esercitazione. Per altre informazioni, vedere l'articolo che descrive come [prevenire gli attacchi di richiesta intersito falsa][prevent-csrf-attacks].

<h2><a name="xsrf"></a>Aggiungere la protezione XSRF</h2>

La richiesta intersito falsa (nota anche come XSRF o CSRF) è un attacco contro applicazioni ospitate sul Web in base al quale un sito Web dannoso può influenzare l'interazione tra un browser client e un sito Web considerato attendibile da tale browser. Questi attacchi sono possibili in quanto i Web browser inviano automaticamente token di autenticazione con ogni richiesta a un sito Web. Un classico esempio è un cookie di autenticazione, ad esempio un ticket di autenticazione basata su form di ASP.NET. Tuttavia, i siti Web che usano un meccanismo di autenticazione persistente, ad esempio Autenticazione di Windows, autenticazione di base e così via, possono essere presi di mira da questi attacchi.

Un attacco XSRF è diverso da un attacco di phishing. Gli attacchi di phishing richiedono un'interazione da parte della vittima. In un attacco di phishing, un sito Web dannoso imita il sito Web di destinazione e la vittima viene indotta a fornire informazioni sensibili all'autore dell'attacco. In un attacco XSRF spesso non è necessaria alcuna interazione da parte della vittima. Al contrario, l'attacco non fa che attendere che il browser invii automaticamente tutti i cookie pertinenti al sito Web di destinazione.

Per altre informazioni, vedere il sito Web relativo all'[Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP) ([richiesta di intersito falsa](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)).

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto **ContactManager**, scegliere **Aggiungi** e quindi fare clic su **Classe**.

2. Assegnare al file il nome  *ValidateHttpAntiForgeryTokenAttribute.cs* e aggiungere il seguente codice:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Helpers;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using System.Web.Mvc;
        namespace ContactManager.Filters
        {
            public class ValidateHttpAntiForgeryTokenAttribute : AuthorizationFilterAttribute
            {
                public override void OnAuthorization(HttpActionContext actionContext)
                {
                    HttpRequestMessage request = actionContext.ControllerContext.Request;
                    try
                    {
                        if (IsAjaxRequest(request))
                        {
                            ValidateRequestHeader(request);
                        }
                        else
                        {
                            AntiForgery.Validate();
                        }
                    }
                    catch (HttpAntiForgeryException e)
                    {
                        actionContext.Response = request.CreateErrorResponse(HttpStatusCode.Forbidden, e);
                    }
                }
                private bool IsAjaxRequest(HttpRequestMessage request)
                {
                    IEnumerable<string> xRequestedWithHeaders;
                    if (request.Headers.TryGetValues("X-Requested-With", out xRequestedWithHeaders))
                    {
                        string headerValue = xRequestedWithHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(headerValue))
                        {
                            return String.Equals(headerValue, "XMLHttpRequest", StringComparison.OrdinalIgnoreCase);
                        }
                    }
                    return false;
                }
                private void ValidateRequestHeader(HttpRequestMessage request)
                {
                    string cookieToken = String.Empty;
                    string formToken = String.Empty;
					IEnumerable<string> tokenHeaders;
                    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
                    {
                        string tokenValue = tokenHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(tokenValue))
                        {
                            string[] tokens = tokenValue.Split(':');
                            if (tokens.Length == 2)
                            {
                                cookieToken = tokens[0].Trim();
                                formToken = tokens[1].Trim();
                            }
                        }
                    }
                    AntiForgery.Validate(cookieToken, formToken);
                }
            }
        }

1. Aggiungere la seguente istruzione  *using* al controller dei contratti per poter accedere all'attributo **[ValidateHttpAntiForgeryToken]**.

	using ContactManager.Filters;

1. Aggiungere l'attributo **[ValidateHttpAntiForgeryToken]** ai metodi POST di **ContactsController** per proteggerlo dalle minacce XSRF. Sarà necessario aggiungerlo ai metodi di azione "PutContact", "PostContact" e **DeleteContact**.

	[ValidateHttpAntiForgeryToken]
        public IHttpActionResult PutContact(int id, Contact contact)
        {

1. Aggiornare la sezione  *Scripts* del file  *Views\Home\Index.cshtml* per poter includere il codice per recuperare i token XSRF.

         @section Scripts {
            @Scripts.Render("~/bundles/knockout")
            <script type="text/javascript">
                @functions{
                   public string TokenHeaderValue()
                   {
                      string cookieToken, formToken;
                      AntiForgery.GetTokens(null, out cookieToken, out formToken);
                      return cookieToken + ":" + formToken;                
                   }
                }

               function ContactsViewModel() {
                  var self = this;
                  self.contacts = ko.observableArray([]);
                  self.addContact = function () {

                     $.ajax({
                        type: "post",
                        url: "api/contacts",
                        data: $("#addContact").serialize(),
                        dataType: "json",
                        success: function (value) {
                           self.contacts.push(value);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }
                     });

                  }
                  self.removeContact = function (contact) {
                     $.ajax({
                        type: "DELETE",
                        url: contact.Self,
                        success: function () {
                           self.contacts.remove(contact);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }

                     });
                  }

                  $.getJSON("api/contacts", function (data) {
                     self.contacts(data);
                  });
               }
               ko.applyBindings(new ContactsViewModel());
            </script>


<h2><a name="bkmk_deploydatabaseupdate"></a>Pubblicare l'aggiornamento dell'applicazione in Azure e nel database SQL</h2>

Per pubblicare l'applicazione, ripetere la procedura seguita in precedenza.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**.

	![Publish][rxP]

5. Fare clic sulla scheda **Impostazioni**.
	

1. In **ContactsManagerContext(ContactsManagerContext)** fare clic sull'icona **v** per sostituire  *Remote connection string* con la stringa di connessione per il database dei contatti. Fare clic su **ContactDB**.

	![Settings](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png)

7. Selezionare la casella **Esegui Migrazioni Code First (esecuzione all'avvio dell'applicazione)**.

1. Fare clic su **Avanti** e quindi su **Anteprima**. In Visual Studio verrà visualizzato un elenco dei file che verranno aggiunti o aggiornati.

8. Fare clic su **Pubblica**.
Al termine della distribuzione, nel browser verrà aperta la pagina iniziale dell'applicazione.

	![Index page with no contacts][intro001]

	Il processo di pubblicazione di Visual Studio ha automaticamente configurato la stringa di connessione nel file  *Web.config* distribuito affinché punti al database SQL. Ha inoltre configurato le migrazioni Code First affinché aggiornino automaticamente il database alla versione più recente la prima volta che l'applicazione accede al database dopo la distribuzione.

	Grazie a questa configurazione, Code First ha creato il database tramite l'esecuzione del codice nella classe **Initial** creata in precedenza. Questa operazione è stata eseguita la prima volta che l'applicazione ha tentato di accedere al database dopo la distribuzione.

9. Immettere un contatto come se l'app fosse eseguita localmente per verificare che la distribuzione del database abbia avuto esito positivo.

Se la voce immessa viene salvata e quindi visualizzata nella pagina di Contact Manager, significa che è stata archiviata nel database.

![Pagina dell'indice con i contatti][addwebapi004]

L'applicazione è ora in esecuzione nel cloud e usa il database SQL per archiviare i relativi dati. Al termine del test dell'applicazione in Azure, eliminarla. L'applicazione è pubblica e non dispone di un meccanismo per limitare l'accesso.

<h2><a name="nextsteps"></a>Passaggi successivi</h2>

Un'applicazione reale richiederebbe l'autenticazione e l'autorizzazione e a tale scopo verrebbe usato un database di appartenenza. L'esercitazione [Distribuire un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure](http://azure.microsoft.com/develop/net/tutorials/web-site-with-sql-database/), basata su questa esercitazione, illustra come distribuire un'applicazione Web con il database di appartenenza.

Un altro modo per archiviare i dati in un'applicazione di Azure consiste nell'usare Archiviazione di Azure, che offre un servizio di archiviazione di dati non relazionali sotto forma di BLOB e tabelle. Per ulteriori informazioni sull'API Web, su ASP.NET MVC e Azure, vedere i collegamenti seguenti.
 

* [Introduzione a Entity Framework con MVC][EFCodeFirstMVCTutorial]
* [Introduzione ad ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Creare la prima API Web ASP.NET](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)
* [Debug di WAWS](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-troubleshoot-visual-studio/)

Questa esercitazione e l'applicazione di esempio sono state scritte da [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) con il supporto di Tom Dykstra e Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)). 

Inviare commenti e suggerimenti sugli aspetti ritenuti utili e su eventuali miglioramenti da apportare, non solo in merito all'esercitazione, ma anche ai prodotti che illustra. I commenti e suggerimenti degli utenti risulteranno utili per definire la priorità dei miglioramenti da apportare. In particolare, saranno apprezzati i commenti relativi all'interesse in merito a un'ulteriore automazione per il processo di configurazione e distribuzione del database di appartenenza. 

<!-- bookmarks -->
[Aggiungere un provider OAuth]: #addOauth
[Aggiungere ruoli al database di appartenenza]:#mbrDB
[Creare uno script di distribuzione dei dati]:#ppd
[Aggiornare il database di appartenenza]:#ppd2
[setupdbenv]: #bkmk_setupdevenv
[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[adddb]: #bkmk_addadatabase
[addcontroller]: #bkmk_addcontroller
[addwebapi]: #bkmk_addwebapi
[deploy2]: #bkmk_deploydatabaseupdate

<!-- links -->
[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[dbcontext-link]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx


<!-- images-->
[rxE]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxE.png
[rxP]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxP.png
[rx22]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/
[rxb2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxb2.png
[rxz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz.png
[rxzz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxzz.png
[rxz2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz2.png
[rxz3]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz3.png
[rxStyle]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxStyle.png
[rxz4]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz4.png
[rxz44]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz44.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxPrevDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPrevDB.png
[rxOverwrite]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxOverwrite.png
[rxPWS]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPWS.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxAddApiController]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxAddApiController.png
[rxFFchrome]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxFFchrome.png
[intro001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxCreateWSwithDB.png
[setup007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-setup-azure-site-004.png
[setup009]: ../Media/dntutmobile-setup-azure-site-006.png
[newapp002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-002.png
[newapp004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-004.png
[firsdeploy007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-005.png
[firsdeploy009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-007.png
[adddb001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-001.png
[adddb002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-context-menu.png
[addcode002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-controller-dialog.png
[addcode004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-index-context.png
[addcode005]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-contents-context-menu.png
[addcode007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-bundleconfig-context.png
[addcode008]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-console.png
[addwebapi004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-added-contact.png
[addwebapi006]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-save-returned-contacts.png
[addwebapi007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-contacts-in-notepad.png
[Aggiungere la protezione XSRF]: #xsrf
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[Add XSRF Protection]: #xsrf
[ImportPublishSettings]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/PublishVSSolution.png
[ValidateConnection]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ValidateConnection.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[prevent-csrf-attacks]: http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-(csrf)-attacks


<!--HONumber=42-->
