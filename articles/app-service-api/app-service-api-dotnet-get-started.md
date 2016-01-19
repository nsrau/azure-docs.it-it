<properties
	pageTitle="Introduzione alle app per le API e ad ASP.NET nel servizio app di Azure | Microsoft Azure"
	description="Informazioni su come creare un'app per le API ASP.NET nel servizio app di Azure con Visual Studio 2015."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="01/05/2016"
	ms.author="tdykstra"/>

# Introduzione alle app per le API e ad ASP.NET nel servizio app di Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Panoramica

In questa esercitazione sarà possibile creare un'[app per le API del servizio app](app-service-api-apps-why-best-platform.md), distribuire un'API Web ASP.NET nell'app per le API e utilizzare l'app per le API da un client MVC ASP.NET. Questa esercitazione presuppone che l'utente abbia familiarità con ASP.NET, ma nessuna esperienza con Microsoft Azure. Al termine dell'esercitazione saranno disponibili un'API Web e un'applicazione client in esecuzione nel cloud.

L'applicazione di esempio è un semplice elenco di contatti. La figura seguente illustra come sono visualizzati i dati ricevuti dall'API nell'app MVC.

![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

## Contenuto dell'esercitazione

Tre funzionalità del servizio App di Azure sono particolarmente utili per lo sviluppo e l'hosting di API:

* Supporto integrato per i metadati dell'API
* Supporto CORS
* Supporto per l'autenticazione e l'autorizzazione
 
Questa è la prima esercitazione della serie che illustra queste funzionalità. Questa esercitazione è incentrata sui metadati dell'API, la seconda è incentrata su CORS, le rimanenti esercitazioni sono incentrate su autenticazione e autorizzazione.

In queste esercitazioni si apprenderà come:

* Preparare il computer per lo sviluppo in Azure installando Azure SDK per .NET.
* Utilizzare le app per le API e le app Web nel servizio app di Azure usando gli strumenti incorporati in Visual Studio 2015.
* Automatizzare l'individuazione di API tramite il pacchetto NuGet Swashbuckle per generare in modo dinamico il file JSON di definizione dell'API Swagger.
* Usare codice client generato automaticamente per utilizzare un'app per le API da un client .NET.
* Usare il portale di Azure per configurare l'endpoint per i metadati dell'app per le API.
* Usare CORS per chiamare un'app per le API da un client JavaScript, quando quest'ultimo appartiene a un dominio diverso rispetto all'API.
* Usare Azure Active Directory (Azure AD) per proteggere un'API dall'accesso non autenticato.
* Utilizzare un'API protetta per gli utenti connessi ad Azure AD.
* Utilizzare un'API protetta usando un'entità servizio di Azure AD.

## Prerequisiti

[AZURE.INCLUDE [prerequisites](../../includes/app-service-api-dotnet-get-started-prereqs.md)]

[AZURE.INCLUDE [set-up-dev-environment](../../includes/install-sdk-2015-2013.md)]

Questa esercitazione richiede Azure SDK per .NET versione 2.8.1 o successiva.

## Panoramica dell'applicazione di esempio

Il codice che verrà distribuito a un'app per le API e un'app Web per questa esercitazione è disponibile nel repository GitHub [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list). La soluzione ContactsList di Visual Studio include i progetti seguenti usati in questa esercitazione:

* **ContactsList.API**: progetto API Web ASP.NET che restituisce un elenco di nomi e indirizzi di posta elettronica. La chiamata iniziale al metodo Get restituisce 3 contatti hardcoded, quindi le chiamate successive ai metodi Post, Put e Delete salvano le modifiche in un file JSON locale.
* **ContactsList.MVC**: client MVC ASP.NET per l'API ContactsList.

Le esercitazioni successive usano altri progetti della stessa soluzione:

* **ContactsList.Angular**: un client AngularJS, per illustrare il supporto di CORS.
* **ContactsList.Angular.AAD**: un client AngularJS, per illustrare l'autenticazione utente.
* **CompanyContacts.API**: un progetto API Web ASP.NET, per illustrare l'autenticazione dell'account del servizio.  

## Scaricare l'applicazione di esempio 

1. Scaricare il repository [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list).

	È possibile [scaricare un file ZIP](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list/archive/master.zip) o clonare il repository nel computer locale.

2. Aprire la soluzione ContactsList in Visual Studio 2015 o 2013.

2. Compilare la soluzione per ripristinare i pacchetti NuGet.

## Usare l'interfaccia utente e i metadati di Swagger

Il supporto per i metadati dell'API [Swagger](http://swagger.io/) 2.0 è integrato nel servizio app di Azure. Ogni app per le API può definire un endpoint dell'URL che restituisce i metadati per l'API in formato JSON Swagger. I metadati restituiti da tale endpoint possono essere usati per generare codice client.

Per fornire i metadati di Swagger 2.0 per un progetto API Web ASP.NET, installare il pacchetto NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle). Swashbuckle usa la reflection per generare dinamicamente i metadati. Il pacchetto NuGet Swashbuckle è già installato nel progetto ContactsList.API scaricato e viene installato quando si crea un nuovo progetto usando il modello di progetto **App per le API di Azure**. In Visual Studio: **File > Nuovo > Progetto > Applicazione Web ASP.NET > App per le API di Azure**.

In questa sezione dell'esercitazione si esaminano i metadati di Swagger 2.0 generati e quindi si prova un'interfaccia utente di test basata sui metadati di Swagger.

2. Impostare il progetto ContactsList.API come progetto di avvio. Non il progetto CompanyContacts.API, che viene usato in una delle esercitazioni successive. 
 
4. Premere F5 per eseguire l'applicazione in modalità debug.

	Il browser viene aperto alla pagina 403 Forbidden.

	![](./media/app-service-api-dotnet-get-started/403.png)

12. Nella barra degli indirizzi del browser aggiungere `swagger/docs/v1` alla fine della riga e quindi premere INVIO. L'URL sarà `http://localhost:51864/swagger/docs/v1`.

	Questo è l'URL predefinito usato da Swashbuckle per restituire i metadati JSON di Swagger 2.0 per l'API. Se si usa Internet Explorer, il browser richiede di scaricare un file v1.json.

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	Se si usa Chrome o Edge, il browser visualizza il file JSON nella finestra del browser.

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	L'esempio seguente illustra la prima sezione dei metadati di Swagger per l'API, con la definizione per il metodo Get. Questi metadati sono alla base dell'interfaccia utente di Swagger che si useranno nei passaggi seguenti. Si useranno anche in una sezione successiva dell'esercitazione per generare automaticamente il codice client.

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ContactsList.API"
		  },
		  "host": "localhost:51864",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/Contacts": {
		      "get": {
		        "tags": [ "Contacts" ],
		        "operationId": "Contacts_Get",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/Contact" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. Chiudere il browser.

3. Nel progetto ContactsList.API in **Esplora soluzioni** aprire il file *App\_Start\\SwaggerConfig.cs*, quindi scorrere in basso fino al codice seguente e rimuovere il commento.

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	Il file SwaggerConfig.cs viene creato quando si installa il pacchetto Swashbuckle in un progetto. Il file fornisce una serie di modi per configurare Swashbuckle.

	Il codice in cui sono stati rimossi i commenti abilita l'interfaccia utente di Swagger che si userà nei passaggi seguenti. Quando si crea un progetto API Web tramite il modello di progetto app per le API, per impostazione predefinita questo codice viene impostato come commento come misura di protezione.

5. Eseguire di nuovo il progetto.

3. Nella barra degli indirizzi del browser aggiungere `swagger` alla fine della riga e quindi premere INVIO. L'URL sarà `http://localhost:51864/swagger`.

4. Quando viene visualizzata la pagina dell'interfaccia utente di Swagger fare clic su **Contacts** per visualizzare i metodi disponibili.

	![](./media/app-service-api-dotnet-get-started/contactsmethods.png)

5. Fare clic su **Get > Try it out**.

	L'interfaccia utente di Swagger chiama il metodo Get ContactsList e visualizza i risultati JSON.

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Fare clic su **Post** e quindi fare clic sulla casella sotto **Model Schema**.

	Selezionando lo schema del modello viene precompilata automaticamente la casella di input in cui è possibile specificare il valore del parametro per il metodo Post.

	![](./media/app-service-api-dotnet-get-started/post.png)

7. Modificare lo script JSON nella casella di input del parametro `contact`, in modo che appaia come l'esempio seguente oppure sostituire con il proprio nome e l'indirizzo di posta elettronica:

		{
		  "CreatedBy": "",
		  "EmailAddress": "carson@contoso.com",
		  "Id": 4,
		  "Name": "Alexander Carson"
		} 

10. Fare clic su **Try it out**.

	L'API ContactsList restituisce HTTP 200 e un corpo della risposta che conferma ciò che è stato aggiunto.

11. Fare clic su **Get > Try it out**.

	La risposta al metodo Get include ora il nuovo contatto.

12. Provare anche i metodi Put, Delete e Get by ID e quindi chiudere il browser.

Swashbuckle funziona con qualsiasi progetto API Web ASP.NET. Se si vuole aggiungere la generazione metadati di Swagger a un progetto esistente, è sufficiente installare il pacchetto Swashbuckle. Per creare un nuovo progetto, usare il modello di progetto ASP.NET **App per le API di Azure** mostrato nella figura seguente.

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

Questo modello crea un progetto di API Web con Swashbuckle installato.

**Nota:** per impostazione predefinita, Swashbuckle può generare ID operazione di Swagger duplicati per i metodi del controller. Ciò si verifica se il controller presenta metodi di overload HTTP, ad esempio: `Get()` e `Get(id)`. Per informazioni su come gestire gli overload, vedere [Personalizzare le definizioni delle API generate da Swashbuckle](app-service-api-dotnet-swashbuckle-customize.md). Se si crea un progetto di API Web in Visual Studio usando il modello App per le API di Azure, al file *SwaggerConfig.cs* viene aggiunto automaticamente codice che genera ID operazione univoci.

## Creare un'app per le API in Azure e distribuire il progetto ContactsList.API nell'app

In questa sezione si usano gli strumenti di Azure integrati nella procedura guidata **Pubblica sito Web** di Visual Studio per creare una nuova app per le API in Azure. Si distribuisce quindi il progetto ContactsList.API nella nuova app per le API e si chiama l'API eseguendo di nuovo, l'interfaccia utente di Swagger, questa volta mentre è in esecuzione nel cloud.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ContactsList.API e quindi scegliere **Pubblica**.

3.  Nel passaggio **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Servizio app di Microsoft Azure**.

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Accedere al proprio account Azure, se non è già stato fatto, o aggiornare le credenziali se sono scadute.

4. Nella finestra di dialogo del servizio app, scegliere la **sottoscrizione** di Azure da usare e quindi fare clic su **Nuovo**.

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

3. Nella scheda **Hosting** della finestra di dialogo **Crea servizio app** fare clic su **Tipo di modifica** e quindi su **App per le API**.

	![](./media/app-service-api-dotnet-get-started/apptype.png)

	Le funzionalità che saranno disponibili per la nuova app non sono determinate dalla scelta del tipo **App per le API**. L'URL di definizione dell'API (che verrà illustrato più avanti in questa esercitazione), il supporto CORS (che verrà illustrato nella prossima esercitazione) e l'autenticazione (che verrà illustrata nelle ultime 3 esercitazioni di questa serie) sono disponibili per le app Web e per le app per dispositivi mobili oltre che per le app per le API. La creazione di un'app come app per le API ha solo gli effetti seguenti:

	a. Nel portale di Azure il testo o l'icona del tipo di app viene visualizzata nelle intestazioni dei pannelli e negli elenchi di app e nel pannello **Impostazioni** la sezione API nell'elenco viene visualizzata prima per un'app per le API che per gli altri tipi di app.

	b. In Visual Studio con Azure SDK per .NET 2.8.1, Visual Studio imposta l'URL di definizione dell'API durante la creazione di una nuova app per le API ASP.NET, ma non per gli altri tipi di app.

4. Immettere un **Nome app per le API** che sia univoco nel dominio *azurewebsites.net*.

	Visual Studio propone un nome univoco aggiungendo una stringa di data e ora al nome del progetto. Se si preferisce, è possibile accettare questo nome.

	Se si immette un nome già usato da un altro utente, a destra, invece di un segno di spunta verde, verrà visualizzato un punto esclamativo rosso e sarà necessario specificare un nome diverso.

	Azure utilizzerà questo nome come prefisso per l'URL dell'applicazione. L'URL completo sarà costituito da questo nome e da *.azurewebsites.net*. Ad esempio, se il nome del sito è `ContactsListAPI`, l'URL sarà `contactslistapi.azurewebsites.net`.

6. Nell'elenco a discesa **Gruppo di risorse** immettere "ContactsListGroup" o un altro nome, se si preferisce.

	Questa casella consente di selezionare un [gruppo di risorse](../azure-preview-portal-using-resource-groups.md) esistente o crearne uno nuovo digitando un nome diverso da qualsiasi gruppo di risorse esistente nella sottoscrizione.

	Per questa esercitazione è consigliabile creare un nuovo gruppo di risorse, per eliminare facilmente tutte le risorse di Azure create per l'esercitazione in un unico passaggio.

4. Fare clic sul pulsante **Nuovo** accanto all'elenco a discesa **Piano di servizio app**.

	![](./media/app-service-api-dotnet-get-started/createas.png)

	Per altre informazioni sui piani di servizio app, vedere [Panoramica approfondita dei piani del servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Nella finestra di dialogo **Configura piano di servizio app** immettere "ContactsListPlan" o un altro nome, se si preferisce.

5. Nell'elenco a discesa **Località** scegliere la località più vicina.

	Questa impostazione specifica il data center di Azure in cui verrà eseguita l'app. Per questa esercitazione, è possibile selezionare qualsiasi area senza riscontrare differenze evidenti, ma per un'app di produzione è consigliabile che il server sia il più vicino possibile ai client che accedono al server per ridurre al minimo la [latenza](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Nell'elenco a discesa **Dimensioni** fare clic su **Gratuito**.

	Per questa esercitazione il piano tariffario gratuito fornirà prestazioni sufficienti.

6. Nella finestra di dialogo **Configura piano di servizio app** fare clic su **OK**.

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. Nella finestra di dialogo **Crea servizio App** fare clic su **Crea**.

	Visual Studio crea l'app per le API e un profilo di pubblicazione che include tutte le impostazioni necessarie per la nuova app per le API. Nei passaggi seguenti usare il nuovo profilo di pubblicazione per distribuire il progetto.
 
	**Nota:** esistono altri modi per creare app per le API nel servizio app di Azure. In Visual Studio le stesse finestre di dialogo sono disponibili quando si crea un nuovo progetto. È anche possibile creare app per le API tramite il portale di Azure, i [cmdlet di Azure per Windows PowerShell](../powershell-install-configure.md) o l'[interfaccia della riga di comando multipiattaforma](../xplat-cli.md).

8. Nella scheda **Connessione** della procedura guidata **Pubblica sito Web** fare clic su **Pubblica**.

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	Visual Studio distribuisce il progetto ContactsList.API nella nuova app per le API e apre un browser all'URL dell'app per le API. Viene visualizzata una pagina che informa che l'operazione di creazione è riuscita.

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. Nella barra degli indirizzi del browser aggiungere "swagger" all'URL e quindi premere INVIO. L'URL sarà `http://{apiappname}.azurewebsites.net/swagger`.

	Il browser visualizza la stessa interfaccia utente di Swagger mostrata in precedenza, ma ora viene eseguita nel cloud. Provare a usare il metodo Get. Si noterà che si è tornati ai 3 contatti predefiniti, perché le modifiche apportate in precedenza sono state salvate in un file locale, mentre quelle apportate ora verranno salvate nel file system dell'app per le API di Azure.

12. Aprire il [portale di Azure](https://portal.azure.com/).
 
14. Fare clic su **Sfoglia > App per le API > {nuova app per le API}**.

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

16. Fare clic su **Impostazioni** e quindi nel pannello **Impostazioni** trovare la sezione API e fare clic su **Definizione API**.

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	Il pannello di definizione dell'API è possibile specificare l'URL che restituisce i metadati di Swagger 2.0 in formato JSON. Quando Visual Studio crea l'app per le API, imposta l'URL di definizione dell'API sul valore predefinito visualizzato in precedenza, ovvero l'URL di base dell'app per le API più `/swagger/docs/v1`.

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	Quando si seleziona un'app per le API per generare il relativo codice client, Visual Studio recupera i metadati dall'URL.

### URL di definizione dell'API negli strumenti di Gestione risorse di Azure

È anche possibile configurare l'URL di definizione dell'API per un'app per le API usando strumenti di Gestione risorse di Azure come Azure PowerShell, CLI o [Esplora risorse](https://resources.azure.com/).

Impostare la proprietà `apiDefinition` sul tipo di risorsa `Microsoft.Web/sites/config` per la risorsa `<site name>/web`. Ad esempio, in **Esplora risorse** passare a **sottoscrizioni > {sottoscrizione} > gruppi di risorse > {gruppo di risorse} > provider > Microsoft.Web > siti > {sito} > config > web** per visualizzare la proprietà `apiDefinition`:

		"apiDefinition": {
		  "url": "https://contactslistapi.azurewebsites.net/swagger/docs/v1"
		}

## <a id="codegen"></a> Utilizzare da un client .NET tramite un codice client generato

Uno dei vantaggi dell'integrazione di Swagger nelle app per le API di Azure è la generazione automatica del codice. Le classi client generate semplificano la scrittura del codice che chiama un'app per le API.

Questa sezione illustra come usare un'app per le API da un'app Web MVC ASP.NET. Si esegue prima il client MVC e l'API Web in locale, quindi si distribuisce il client in una nuova app Web nel servizio app di Azure e lo si esegue nel cloud.

### Generare il codice client

È possibile generare il codice client per un'app per le API tramite Visual Studio o dalla riga di comando. Per questa esercitazione si userà Visual Studio. Per informazioni su come eseguire questa operazione dalla riga di comando, vedere il file Leggimi del repository [Azure/autorest](https://github.com/azure/autorest) in GitHub.com.

Il progetto ContactsList.MVC include già il codice client generato, ma sarà eliminato e rigenerato per impostare l'URL di destinazione predefinito sulla propria app per le API.

1. In **Esplora soluzioni** di Visual Studio eliminare la cartella *ContactsList.API* nel progetto ContactsList.MVC.

	Questa cartella è stata creata con il processo di generazione di codice illustrato di seguito.

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. Fare clic con il pulsante destro del mouse sul progetto ContactsList.MVC e quindi scegliere **Aggiungi > Client API REST**.

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Nella finestra di dialogo **Aggiungi client API REST** fare clic su **Scarica dall'app per le API di Microsoft Azure** e quindi su **Sfoglia**.

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. Nella finestra di dialogo **Servizio app** espandere il gruppo di risorse **ContactsListGroup**, selezionare l'app per le API e quindi fare clic su **OK**.

	Questa finestra di dialogo offre più modi per organizzare le app per le API nell'elenco, nel caso ne siano presenti troppe da scorrere. Consente anche di immettere una stringa di ricerca per filtrare le app per le API per nome.

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	Se l'app per le API non viene visualizzata nell'elenco, durante la creazione dell'app per le API il tipo potrebbe non essere stato modificato da app Web in app per le API. In tal caso, è possibile creare una nuova app per le API ripetendo i passaggi eseguiti in precedenza. È necessario scegliere un nome diverso per l'app per le API, a meno che prima non venga eliminata l'app Web dal portale.

	Quando si torna alla finestra di dialogo **Aggiungi client API REST**, la casella di testo risulta già compilata con il valore dell'URL di definizione dell'API visualizzato in precedenza nel portale.

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	Un modo alternativo per ottenere i metadati per la generazione del codice consiste nell'immettere l'URL direttamente invece che tramite la finestra di dialogo Sfoglia. Ad esempio, se l'API è stata distribuita in un'app Web e non è visualizzata nella finestra di dialogo Sfoglia, è possibile immettere manualmente qui l'URL che restituisce i metadati di Swagger.

	Un altro modo alternativo per ottenere i metadati consiste nell'usare l'opzione **Selezionare un file di metadati Swagger esistente**. Ad esempio, per generare il codice client prima di distribuirlo in Azure, è possibile eseguirlo in locale, scaricare il file JSON per Swagger e selezionarlo qui.

9. Nella finestra di dialogo **Aggiungi client API REST** fare clic su **OK**.

	Visual Studio crea una cartella con il nome dell'app per le API e genera classi client.

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. Aprire *Controllers\\ContactsController.cs* per visualizzare il codice che chiama l'API tramite il client generato.

	Il frammento seguente viene illustrato come creare un'istanza dell'oggetto client e chiamare il metodo Get.

		private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	Questo codice passa l'URL di IIS Express locale del progetto API al costruttore della classe client per consentire l'esecuzione locale del progetto Web MVC e del progetto API. Se si omette il parametro del costruttore, l'endpoint predefinito sarà l'URL da cui è stato generato il codice.

6. La classe del client verrà generata con un nome diverso basato sul nome dell'app per le API. Modificare il codice in modo che il nome del tipo corrisponde a quello generato nel progetto. Ad esempio, se l'app per le API è stata denominata ContactsListAPIContoso, il codice sarà simile all'esempio seguente:

		private ContactsListAPIContoso db = new ContactsListAPIContoso(new Uri("http://localhost:51864"));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

7. Compilare la soluzione.

Il controller e le visualizzazioni del progetto MVC sono simili al controller e alle visualizzazioni di cui è stato eseguito lo scaffolding per Entity Framework, perché è così che sono stati creati, ovvero eseguendo lo scaffolding di un modello di dati di Entity Framework e quindi apportando piccole modifiche per usare il client API REST invece di un contesto di database di Entity Framework.

### Esecuzione locale

1. Impostare i progetti ContactsList.API e ContactsList.MVC come progetti di avvio, con ContactsList.API avviato prima di ContactsList.MVC. Fare clic con il pulsante destro del mouse sulla soluzione, scegliere **Proprietà**, **Progetti di avvio multipli** e impostare ogni progetto su **Avvio**. Usare le icone delle frecce su/giù per assicurarsi che ContactsList.API sia il primo dell'elenco. 

2. Premere F5 per avviare il progetto.

	Un browser visualizza la pagina 403 per l'API e uno visualizza la home page dell'app MVC.

3. Fare clic su **Contatti** nella barra dei menu del browser che visualizza la home page dell'app MVC.

	L'interfaccia utente di MVC visualizza i contatti archiviati in locale e può essere usata per aggiungere ed eliminare contatti.

	![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

### Creare un'app Web in Azure e distribuire il progetto ContactsList.MVC nell'app

In questa sezione si userà lo stesso metodo per creare un'app Web usato in precedenza per creare un'app per le API e lo stesso metodo per distribuire un progetto Web nell'app Web di Azure.

#### Modificare il progetto MVC in modo che punti all'app per le API di Azure 

Prima della distribuzione in Azure, modificare l'endpoint dell'API nel progetto MVC in modo che quando il codice viene distribuito chiami l'app per le API di Azure creata in precedenza invece di localhost.

1. Nel progetto ContactsList.MVC aprire *Controllers/ContactsController.cs*.

2. Impostare come commento la riga che imposta l'URL di base dell'API sull'URL localhost e rimuovere il commento dalla riga che non include parametri del costruttore. Il codice sarà simile all'esempio seguente, ma in entrambe le righe il nome della classe riflette il nome dell'app per le API.

		private ContactsListAPI db = new ContactsListAPI();
		//private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));

	L'URL di destinazione predefinito è l'app per le API di Azure, perché è l'origine da cui è stato generato il codice. Se è stato usato un metodo diverso per generare il codice, potrebbe essere necessario specificare l'URL dell'app per le API di Azure nello stesso modo con cui si è specificato l'URL locale.

#### Creare un'app Web per ospitare il sito MVC

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ContactsList.MVC e quindi scegliere **Pubblica**.

2. Nella procedura guidata **Pubblica sito Web** fare clic sulla scheda **Profilo**.

3.  Nella scheda **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Servizio app di Microsoft Azure**.

5. Nella finestra di dialogo **Servizio App** fare clic su **Nuovo**.

3. Nella scheda **Hosting** della finestra di dialogo **Crea servizio app** fare clic su **Tipo di modifica** e verificare che il tipo sia **App Web**.

4. Immettere un **Nome app Web** che sia univoco nel dominio *azurewebsites.net*.

5. Scegliere la **Sottoscrizione** di Azure da usare.

6. Nell'elenco a discesa **Gruppo di risorse** selezionare il nome del gruppo di risorse creato in precedenza.

4. Nell'elenco a discesa **Piano di servizio app** scegliere lo stesso piano creato in precedenza.

7. Fare clic su **Crea**.

	Visual Studio crea l'app Web, crea un profilo di pubblicazione per l'app e visualizza il passaggio **Connessione** della procedura guidata **Pubblica sito Web**.

### Distribuire il progetto ContactsList.Web nella nuova app Web

3.  Nel passaggio **Connessione** della procedura guidata **Pubblica sito Web** fare clic su **Pubblica**.

	Visual Studio distribuisce il progetto ContactsList.MVC nella nuova app Web e apre un browser all'URL dell'app Web. Viene visualizzata la stessa interfaccia utente di MVC precedentemente eseguita in locale, ma che ora visualizza i contatti archiviati nel file system dell'app per le API di Azure.

	![](./media/app-service-api-dotnet-get-started/codegencontacts.png)

## Passaggi successivi

Questa esercitazione ha illustrato come creare app per le API, distribuire il codice in queste app, generare il relativo codice client e usarle dai client .NET. L'esercitazione successiva della serie di esercitazioni introduttive sulle app per le API mostra come [utilizzare app per le API da client JavaScript tramite CORS](app-service-api-cors-consume-javascript.md).

<!---HONumber=AcomDC_0114_2016-->