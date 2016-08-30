<properties
	pageTitle="Introduzione alle app per le API e ad ASP.NET nel servizio app | Microsoft Azure"
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
	ms.date="08/19/2016"
	ms.author="rachelap"/>

# Introduzione alle app per le API, ad ASP.NET e a Swagger in Servizio app di Azure

[AZURE.INCLUDE [selettore](../../includes/app-service-api-get-started-selector.md)]

Questa è la prima di una serie di esercitazioni che illustrano come usare le funzionalità del servizio app di Azure utili per lo sviluppo e l'hosting di API RESTful. Questa esercitazione illustra il supporto per i metadati dell'API in formato Swagger.

Si apprenderà come:

* Creare e distribuire [app per le API](app-service-api-apps-why-best-platform.md) in Servizio app di Azure usando gli strumenti predefiniti di Visual Studio 2015.
* Automatizzare l'individuazione di API tramite il pacchetto NuGet Swashbuckle per generare in modo dinamico i metadati dell'API Swagger.
* Usare i metadati dell'API Swagger per generare automaticamente il codice client per un'app per le API.

## Panoramica dell'applicazione di esempio

In questa esercitazione si usa una semplice applicazione di esempio di elenco attività. L'applicazione ha un front-end costituito da un'applicazione a singola pagina, un livello intermedio con un'API Web ASP.NET e un livello dati con un'API Web ASP.NET.

![Diagramma dell'applicazione di esempio di app per le API](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

Di seguito è riportata una schermata del front-end [AngularJS](https://angularjs.org/).

![Elenco attività dell'applicazione di esempio app per le API](./media/app-service-api-dotnet-get-started/todospa.png)

La soluzione di Visual Studio contiene tre progetti:

![](./media/app-service-api-dotnet-get-started/projectsinse.png)

* **ToDoListAngular**: front-end. Un'applicazione a singola pagina AngularJS che chiama il livello intermedio.

* **ToDoListAPI**: livello intermedio. Un progetto API Web ASP.NET che chiama il livello dati per eseguire operazioni CRUD sulle attività.

* **ToDoListDataAPI**: livello dati. Un progetto API Web ASP.NET che esegue operazioni CRUD sulle attività.

L'architettura a tre livelli è una delle tante che è possibile implementare usando le app per le API e viene usata qui solo a scopo illustrativo. Il codice in ogni livello è quello più semplice possibile per illustrate le funzionalità delle app per le API. Il livello dati, ad esempio, usa la memoria del server invece di un database come meccanismo di salvataggio permanente.

Al termine di questa esercitazione, i due progetti di API Web saranno in esecuzione nel cloud tra le app per le API del servizio app.

L'esercitazione successiva della serie distribuisce il front-end dell'applicazione a singola pagina nel cloud.

## Prerequisiti

* API Web ASP.NET: le istruzioni dell'esercitazione presuppongono che l'utente abbia una conoscenza di base dell'uso dell'[API Web 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) ASP.NET in Visual Studio.

* Account Azure: è possibile [aprire un account Azure gratuito](/pricing/free-trial/?WT.mc_id=A261C142F) o [attivare i benefici della sottoscrizione di Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

	Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, passare alla pagina [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751). In questa pagina è possibile creare immediatamente un'app iniziale temporanea nel servizio app. Non è richiesta una carta di credito, né occorre impegnarsi in alcun modo.

* Visual Studio 2015 con [Azure SDK per .NET](http://go.microsoft.com/fwlink/?linkid=518003): l'SDK installa Visual Studio 2015 automaticamente se non è già disponibile.

	>[AZURE.NOTE] In base al numero di dipendenze da SDK già presenti nel computer, l'installazione dell'SDK può richiedere tempi lunghi, da alcuni minuti ad almeno mezz'ora.

## Scaricare l'applicazione di esempio 

1. Scaricare il repository [Azure-Samples/app-service-api-dotnet-to-do-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list).

	È possibile fare clic sul pulsante **Download ZIP** o clonare il repository nel computer locale.

2. Aprire la soluzione ToDoList in Visual Studio 2015 o 2013.

2. Compilare la soluzione per ripristinare i pacchetti NuGet.

	Per osservare l'applicazione in esecuzione prima di distribuirla, è possibile eseguirla in locale. È sufficiente verificare che tutti e tre i progetti siano progetti di avvio. Sarà necessario usare Internet Explorer o Microsoft Edge perché tali browser consentono le chiamate JavaScript tra le origini agli URL `http://localhost`.

## Usare l'interfaccia utente e i metadati dell'API Swagger

Il supporto per i metadati dell'API [Swagger](http://swagger.io/) 2.0 è integrato nel servizio app di Azure. Ogni app per le API può specificare un endpoint dell'URL che restituisce i metadati per l'API in formato JSON Swagger. I metadati restituiti da tale endpoint possono essere usati per generare codice client.

Un progetto API Web ASP.NET può generare in modo dinamico i metadati di Swagger usando il pacchetto NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle). Il pacchetto NuGet Swashbuckle è già installato in ToDoListDataAPI e nei progetto ToDoListAPI scaricati.

In questa sezione dell'esercitazione si esaminano i metadati di Swagger 2.0 generati e quindi si prova un'interfaccia utente di test basata sui metadati di Swagger.

2. Impostare il progetto ToDoListDataAPI come progetto di avvio, **non** come progetto ToDoListAPI.
 
4. Premere F5 o fare clic su **Debug > Avvia debug** per eseguire il progetto in modalità debug.

	Verrà aperto il browser alla pagina di errore HTTP 403.

12. Nella barra degli indirizzi del browser aggiungere `swagger/docs/v1` alla fine della riga e quindi premere INVIO. L'URL è `http://localhost:45914/swagger/docs/v1`.

	Questo è l'URL predefinito usato da Swashbuckle per restituire i metadati JSON di Swagger 2.0 per l'API.

	Se si usa Internet Explorer, il browser richiede di scaricare un file *v1.json*.

	![Scaricare i metadati JSON in Internet Explorer](./media/app-service-api-dotnet-get-started/iev1json.png)

	Se si usa Chrome, Firefox o Microsoft Edge, il browser visualizza il codice JSON nella finestra del browser. Ogni browser gestisce JSON in modo diverso e la finestra del browser potrebbe non essere esattamente uguale a quella dell'esempio.

	![Metadati JSON in Chrome](./media/app-service-api-dotnet-get-started/chromev1json.png)

	L'esempio seguente illustra la prima sezione dei metadati di Swagger per l'API, con la definizione per il metodo Get. Questi metadati sono alla base dell'interfaccia utente di Swagger che vengono usati nei passaggi seguenti e anche in una sezione successiva dell'esercitazione per generare automaticamente il codice client.

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ToDoListDataAPI"
		  },
		  "host": "localhost:45914",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/ToDoList": {
		      "get": {
		        "tags": [ "ToDoList" ],
		        "operationId": "ToDoList_GetByOwner",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "parameters": [
		          {
		            "name": "owner",
		            "in": "query",
		            "required": true,
		            "type": "string"
		          }
		        ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/ToDoItem" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. Chiudere il browser e arrestare il debug di Visual Studio.

3. Nel progetto ToDoListDataAPI in **Esplora soluzioni** aprire il file *App\_Start\\SwaggerConfig.cs*, quindi scorrere in basso fino al codice seguente e rimuovere il commento.

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	Il file *SwaggerConfig.cs* viene creato quando si installa il pacchetto Swashbuckle in un progetto. Il file fornisce una serie di modi per configurare Swashbuckle.

	Il codice in cui sono stati rimossi i commenti abilita l'interfaccia utente di Swagger che viene usata nei passaggi seguenti. Quando si crea un progetto API Web con il modello di progetto app per le API, questo codice è impostato come commento per impostazione predefinita come misura di sicurezza.

5. Eseguire di nuovo il progetto.

3. Nella barra degli indirizzi del browser aggiungere `swagger` alla fine della riga e quindi premere INVIO. L'URL è `http://localhost:45914/swagger`.

4. Quando viene visualizzata la pagina dell'interfaccia utente di Swagger, fare clic su **ToDoList** per visualizzare i metodi disponibili.

	![Metodi disponibili dell'interfaccia utente di Swagger](./media/app-service-api-dotnet-get-started/methods.png)

5. Fare clic sul primo pulsante **Get** nell'elenco.

6. Nella sezione **Parameters** immettere un asterisco come valore del parametro `owner` e quindi fare clic su **Try it out**.

	Aggiungendo l'autenticazione nelle esercitazioni successive, il livello intermedio fornirà l'ID utente effettivo al livello dati. Per ora, il campo del proprietario mostrerà un asterisco per tutte le attività mentre l'applicazione viene eseguita senza autenticazione abilitata.

	![Prova dell'interfaccia utente di Swagger](./media/app-service-api-dotnet-get-started/gettryitout1.png)

	L'interfaccia utente di Swagger chiama il metodo Get ToDoList e visualizza il codice di risposta e i risultati JSON.

	![Risultati della prova dell'interfaccia utente di Swagger](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Fare clic su **Post** e quindi fare clic sulla casella sotto **Model Schema**.

	Selezionando lo schema del modello viene precompilata automaticamente la casella di input in cui è possibile specificare il valore del parametro per il metodo Post. Se questo non funziona in Internet Explorer, usare un browser diverso o immettere manualmente il valore del parametro nel passaggio successivo.

	![Post della prova dell'interfaccia utente di Swagger](./media/app-service-api-dotnet-get-started/post.png)

7. Modificare il codice JSON nella casella di input del parametro `todo`, in modo che appaia come l'esempio seguente, oppure sostituire con un testo descrittivo personalizzato:

		{
		  "ID": 2,
		  "Description": "buy the dog a toy",
		  "Owner": "*"
		}

10. Fare clic su **Try it out**.

	L'API ToDoList restituisce un codice di risposta HTTP 204 che indica l'esito positivo.

11. Fare clic sul primo pulsante **Get** e quindi in quella sezione della pagina fare clic sul pulsante **Try it out**.

	La risposta al metodo Get ora include la nuova attività.

12. Facoltativo: provare anche i metodi Put, Delete e Get by ID.

14. Chiudere il browser e arrestare il debug di Visual Studio.

Swashbuckle funziona con qualsiasi progetto API Web ASP.NET. Se si vuole aggiungere la generazione metadati di Swagger a un progetto esistente, è sufficiente installare il pacchetto Swashbuckle.

**Nota:** i metadati di Swagger includono un ID univoco per ogni operazione API. Per impostazione predefinita, Swashbuckle può generare ID operazione di Swagger duplicati per i metodi del controller dell'API Web. Ciò si verifica se il controller presenta metodi di overload HTTP, ad esempio `Get()` e `Get(id)`. Per informazioni su come gestire gli overload, vedere [Personalizzare le definizioni delle API generate da Swashbuckle](app-service-api-dotnet-swashbuckle-customize.md). Se si crea un progetto di API Web in Visual Studio usando il modello App per le API di Azure, al file *SwaggerConfig.cs* viene aggiunto automaticamente codice che genera ID operazione univoci.

## <a id="createapiapp"></a> Creare un'app per le API in Azure e distribuirvi il codice

In questa sezione si usano gli strumenti di Azure integrati nella procedura guidata **Pubblica sul Web** di Visual Studio per creare una nuova app per le API in Azure. Si distribuisce quindi il progetto ToDoListDataAPI nella nuova app per le API e si chiama l'API eseguendo l'interfaccia utente di Swagger.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ToDoListDataAPI e quindi scegliere **Pubblica**.

	![Fare clic su Pubblica in Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu.png)

3.  Nel passaggio **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Servizio app di Microsoft Azure**.

	![Fare clic su Servizio app di Azure in Pubblica sul Web](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Accedere al proprio account Azure, se non è già stato fatto, o aggiornare le credenziali se sono scadute.

4. Nella finestra di dialogo Servizio app scegliere la **sottoscrizione** di Azure da usare e quindi fare clic su **Nuovo**.

	![Fare clic su Nuovo nella finestra di dialogo Servizio app](./media/app-service-api-dotnet-get-started/clicknew.png)

	Viene visualizzata la scheda **Hosting** della finestra di dialogo **Crea servizio app**.

	Poiché si sta distribuendo un progetto API Web con Swashbuckle installato, Visual Studio presuppone che si voglia creare un'app per le API come è indicato dal titolo **Nome app per le API** e dal fatto che l'elenco a discesa **Modifica tipo** è impostato su **App per le API**.

	![Tipo di app nella finestra di dialogo Servizio app](./media/app-service-api-dotnet-get-started/apptype.png)

4. Immettere un **Nome app per le API** che sia univoco nel dominio *azurewebsites.net*. È possibile accettare il nome predefinito proposto da Visual Studio.

	Se si immette un nome già usato da qualcun altro, viene visualizzato un punto esclamativo a destra.

	L'URL dell'app per le API sarà `{API app name}.azurewebsites.net`.

6. Nell'elenco a discesa **Gruppo di risorse** fare clic su **Nuovo** e quindi immettere "ToDoListGroup" o un altro nome, se si preferisce.

	Un gruppo di risorse è una raccolta di risorse di Azure, ad esempio app per le api, database, VM e così via. Per questa esercitazione è consigliabile creare un nuovo gruppo di risorse, per eliminare facilmente tutte le risorse di Azure create per l'esercitazione in un unico passaggio.

	Questa casella consente di selezionare un [gruppo di risorse](../resource-group-overview.md) esistente o crearne uno nuovo digitando un nome diverso da qualsiasi gruppo di risorse esistente nella sottoscrizione.

4. Fare clic sul pulsante **Nuovo** accanto all'elenco a discesa **Piano di servizio app**.

	Lo screenshot mostra i valori di esempio per **API App Name** (Nome app per le API), **Sottoscrizione** e **Gruppo di risorse**. I valori dell'utente saranno diversi.

	![Finestra di dialogo Crea servizio app](./media/app-service-api-dotnet-get-started/createas.png)

	Nei passaggi seguenti si crea un piano di servizio app per il nuovo gruppo di risorse. Un piano di servizio app specifica le risorse di calcolo in cui viene eseguita l'app per le API. Se, ad esempio, si sceglie il livello gratuito, l'app per le API viene eseguita in VM condivise, mentre con alcuni livelli a pagamento viene eseguita in VM dedicate. Per altre informazioni sui piani di servizio app, vedere [Panoramica approfondita dei piani del servizio app di Azure](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Nella finestra di dialogo **Configura piano di servizio app** immettere "ToDoListPlan" o un altro nome, se si preferisce.

5. Nell'elenco a discesa **Località** scegliere la località più vicina.

	Questa impostazione specifica il data center di Azure in cui verrà eseguita l'app. Scegliere una località vicina per ridurre al minimo la [latenza](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Nell'elenco a discesa **Dimensioni** fare clic su **Gratuito**.

	Per questa esercitazione il piano tariffario Gratuito fornirà prestazioni sufficienti.

6. Nella finestra di dialogo **Configura piano di servizio app** fare clic su **OK**.

	![Fare clic su OK in Configura piano di servizio app](./media/app-service-api-dotnet-get-started/configasp.png)

7. Nella finestra di dialogo **Crea servizio App** fare clic su **Crea**.

	![Fare clic su Crea nella finestra di dialogo Crea servizio app](./media/app-service-api-dotnet-get-started/clickcreate.png)

	Visual Studio crea l'app per le API e un profilo di pubblicazione che include tutte le impostazioni necessarie per l'app per le API. Apre quindi la procedura guidata **Pubblica sul Web** che si userà per distribuire il progetto.

	All'apertura della procedura guidata **Pubblica sul Web** viene visualizzata la scheda **Connessione**, come illustrato di seguito.

	Nella scheda **Connessione** le impostazioni per **Server** e **Nome sito** fanno riferimento all'app per le API. I valori di **Nome utente** e **Password** sono credenziali di distribuzione create automaticamente da Azure. Al termine della distribuzione Visual Studio apre un browser all'**URL di destinazione**. Questo è il solo scopo dell'**URL di destinazione**.

8. Fare clic su **Avanti**.

	![Fare clic su Avanti nella scheda Connessione di Pubblica sul Web](./media/app-service-api-dotnet-get-started/connnext.png)

	La scheda successiva è **Impostazioni**, illustrata di seguito, in cui è possibile modificare la configurazione della build per distribuire una build di debug per il [debug remoto](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remotedebug). La scheda include anche numerose **Opzioni pubblicazione file**:

	* Rimuovi i file aggiuntivi nella destinazione
	* Precompila durante la pubblicazione
	* Escludi file dalla cartella App\_Data

	Per questa esercitazione non ne è necessaria nessuna. Per una descrizione dettagliata del funzionamento di queste opzioni, vedere [Procedura: Distribuire un progetto di applicazione Web tramite la pubblicazione con un clic in Visual Studio](https://msdn.microsoft.com/library/dd465337.aspx).

14. Fare clic su **Avanti**.

	![Fare clic su Avanti nella scheda Impostazioni di Pubblica sul Web](./media/app-service-api-dotnet-get-started/settingsnext.png)

	Si passa quindi alla scheda **Anteprima**, illustrata di seguito, che consente di visualizzare quali file verranno copiati dal progetto all'app per le API. Quando si distribuisce un progetto in un'app per le API che è già stato distribuito prima, vengono copiati solo i file modificati. Per visualizzare un elenco di quelli che verranno copiati, è possibile fare clic sul pulsante **Avvia anteprima**.

15. Fare clic su **Pubblica**.

	![Fare clic su Pubblica nella scheda Anteprima di Pubblica sul Web](./media/app-service-api-dotnet-get-started/clickpublish.png)

	Visual Studio distribuisce il progetto ToDoListDataAPI nella nuova app per le API. La finestra **Output** registra la distribuzione riuscita e una pagina indicante che la creazione è avvenuta correttamente viene visualizzata in una finestra del browser aperta all'indirizzo dell'URL dell'app per le API.

	![Finestra di output che segnala che la distribuzione è completata](./media/app-service-api-dotnet-get-started/deploymentoutput.png)

	![Pagina che segnala che la creazione dell'app per le API è completata](./media/app-service-api-dotnet-get-started/appcreated.png)

11. Nella barra degli indirizzi del browser aggiungere "swagger" all'URL e quindi premere INVIO. L'URL è `http://{apiappname}.azurewebsites.net/swagger`.

	Il browser visualizza la stessa interfaccia utente di Swagger mostrata in precedenza, ma ora viene eseguita nel cloud. Provando a usare il metodo Get le attività predefinite tornano a essere due. Le modifiche apportate in precedenza sono state salvate in memoria nel computer locale.

12. Aprire il [portale di Azure](https://portal.azure.com/).

	Il portale di Azure è un'interfaccia Web per la gestione delle risorse di Azure, ad esempio le app per le API.
 
14. Fare clic su **Sfoglia > Servizi app**.

	![Selezionare Servizi app](./media/app-service-api-dotnet-get-started/browseas.png)

15. Nel pannello **Servizi app** trovare la nuova app per le API e fare clic su di essa. Nel portale di Azure le finestre che si aprono sulla destra sono dette *pannelli*.

	![Pannello Servizi app](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

	Vengono aperti due pannelli, uno con una panoramica dell'app per le API e uno con un lungo elenco di impostazioni che è possibile visualizzare e modificare.

16. Nel pannello **Impostazioni** trovare la sezione **API** e fare clic su **Definizione API**.

	![Definizione dell'API nel pannello Impostazioni](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	Il pannello **Definizione API** consente di specificare l'URL che restituisce i metadati di Swagger 2.0 in formato JSON. Quando Visual Studio crea l'app per le API, imposta l'URL di definizione dell'API sul valore predefinito per i metadati generati da Swashbuckle visualizzati in precedenza, ovvero l'URL di base dell'app per le API più `/swagger/docs/v1`.

	![URL di definizione dell'API](./media/app-service-api-dotnet-get-started/apidefurl.png)

	Quando si seleziona un'app per le API per generare il relativo codice client, Visual Studio recupera i metadati dall'URL.

## <a id="codegen"></a> Generare il codice client per il livello dati

Uno dei vantaggi dell'integrazione di Swagger nelle app per le API di Azure è la generazione automatica del codice. Le classi client generate semplificano la scrittura del codice che chiama un'app per le API.

Il progetto ToDoListAPI include già il codice client generato, ma nei passaggi seguenti è necessario eliminarlo e rigenerarlo per sapere come eseguire la generazione del codice.

1. Nel progetto ToDoListAPI in **Esplora soluzioni** di Visual Studio eliminare la cartella *ToDoListDataAPI*. **Attenzione: eliminare solo la cartella, non il progetto ToDoListDataAPI.**

	![Eliminare il codice client generato](./media/app-service-api-dotnet-get-started/deletecodegen.png)

	Questa cartella è stata creata con il processo di generazione di codice illustrato di seguito.

2. Fare clic con il pulsante destro del mouse sul progetto ToDoListAPI e quindi scegliere **Aggiungi > Client dell'API REST**.

	![Aggiungere il client API REST in Visual Studio](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Nella finestra di dialogo **Aggiungi il client dell'API REST** fare clic su **URL Swagger** e quindi su **Seleziona asset di Azure**.

	![Selezionare l'asset Azure](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. Nella finestra di dialogo **Servizio app** espandere il gruppo di risorse usato per questa esercitazione, selezionare l'app per le API e quindi fare clic su **OK**.

	![Selezionare l'app per le API per la generazione di codice](./media/app-service-api-dotnet-get-started/codegenselect.png)

	Quando si torna alla finestra di dialogo **Aggiungi client API REST**, la casella di testo risulta già compilata con il valore dell'URL di definizione dell'API visualizzato in precedenza nel portale.

	![URL di definizione dell'API](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	>[AZURE.TIP] Un modo alternativo per ottenere i metadati per la generazione del codice consiste nell'immettere l'URL direttamente invece che tramite la finestra di dialogo Sfoglia. In alternativa, per generare il codice client prima di distribuirlo in Azure, è possibile eseguire il progetto API Web in locale, andare all'URL che fornisce il file JSON per Swagger e usare l'opzione **Selezionare un file di metadati Swagger esistente**.

9. Nella finestra di dialogo **Aggiungi client API REST** fare clic su **OK**.

	Visual Studio crea una cartella con il nome dell'app per le API e genera classi client.

	![File di codice per il client generato](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. Nel progetto ToDoListAPI aprire *Controllers\\ToDoListController.cs* per visualizzare il codice che chiama l'API usando il client generato.

	Il frammento seguente illustra come il codice crea un'istanza dell'oggetto client e chiama il metodo Get.

		private static ToDoListDataAPI NewDataAPIClient()
		{
		    var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		    return client;
		}
		
		public async Task<IEnumerable<ToDoItem>> Get()
		{
		    using (var client = NewDataAPIClient())
		    {
		        var results = await client.ToDoList.GetByOwnerAsync(owner);
		        return results.Select(m => new ToDoItem
		        {
		            Description = m.Description,
		            ID = (int)m.ID,
		            Owner = m.Owner
		        });
		    }
		}

	Il parametro del costruttore ottiene l'URL dell'endpoint dall'impostazione `toDoListDataAPIURL` dell'app. Nel file Web.config questo valore è impostato sull'URL di IIS Express locale del progetto API per consentire l'esecuzione dell'applicazione in locale. Se si omette il parametro del costruttore, l'endpoint predefinito sarà l'URL da cui è stato generato il codice.

6. La classe del client verrà generata con un nome diverso basato sul nome app per le API. Modificare il codice in *Controllers\\ToDoListController.cs* in modo che il nome del tipo corrisponda a quanto generato nel progetto. Se, ad esempio, si è assegnato all'app per le API il nome ToDoListDataAPI071316, si sostituirà questo codice:

		private static ToDoListDataAPI NewDataAPIClient()
		{
		    var client = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));

con questo:

		private static ToDoListDataAPI071316 NewDataAPIClient()
		{
		    var client = new ToDoListDataAPI071316(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));


## Creare un'app per le API per ospitare il livello intermedio

Prima è stata [creata l'app per le API di livello dati in cui si è distribuito il codice](#createapiapp). Ora si seguirà la stessa procedura per l'app per le API di livello intermedio.

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto ToDoListAPI di livello intermedio, non su ToDoListDataAPI del livello dati, e quindi scegliere **Pubblica**.

	![Fare clic su Pubblica in Visual Studio](./media/app-service-api-dotnet-get-started/pubinmenu2.png)

3.  Nella scheda **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Servizio app di Microsoft Azure**.

5. Nella finestra di dialogo **Servizio App** fare clic su **Nuovo**.

3. Nella scheda **Hosting** della finestra di dialogo **Crea servizio app** accettare il **Nome app per le API** predefinito o immettere un nome che sia univoco nel dominio *azurewebsites.net*.

5. Scegliere la **Sottoscrizione** di Azure in uso.

6. Nell'elenco a discesa **Gruppo di risorse** selezionare il nome del gruppo di risorse creato in precedenza.

4. Nell'elenco a discesa **Piano di servizio app** scegliere lo stesso piano creato in precedenza. Tale valore sarà l'impostazione predefinita.

7. Fare clic su **Create**.

	Visual Studio crea l'app per le API e un profilo di pubblicazione per l'app, quindi visualizza il passaggio **Connessione** della procedura guidata **Pubblica sul Web**.

3.  Nel passaggio **Connessione** della procedura guidata **Pubblica sito Web** fare clic su **Pubblica**.

	Visual Studio distribuisce il progetto ToDoListAPI nella nuova app per le API e apre una finestra del browser all'URL dell'app per le API. Viene visualizzata una pagina che informa che l'operazione è riuscita.

## Configurare il livello intermedio per chiamare il livello dati

Se l'app per le API di livello intermedio venisse chiamata ora, cercherebbe di chiamare il livello dati usando l'URL localhost che si trova ancora nel file Web.config. In questa sezione si immette l'URL dell'app per le API del livello dati in un'impostazione dell'ambiente nell'app per le API del livello intermedio. Quando il codice nell'app per le API del livello intermedio recupera l'impostazione dell'URL del livello dati, l'impostazione dell'ambiente esegue l'override del contenuto del file Web.config.
 
1. Accedere al [portale di Azure](https://portal.azure.com/) e passare al pannello **App per le API** relativo all'app per le API creata per ospitare il progetto TodoListAPI (livello intermedio).

2. Nel pannello **Impostazioni** dell'app per le API fare clic su **Impostazioni applicazione**.
 
4. Nel pannello **Impostazioni applicazione** dell'app per le API scorrere verso il basso fino alla sezione **Impostazioni app** e aggiungere la chiave e il valore seguenti:

	| **Chiave** | toDoListDataAPIURL |
	|---|---|
	| **Valore** | https://{your dell'app per le API del livello dati}.azurewebsites.net |
	| **Esempio** | https://todolistdataapi.azurewebsites.net |

4. Fare clic su **Save**.

	![Fare clic su Salva per le impostazioni dell'app](./media/app-service-api-dotnet-get-started/asinportal.png)

	Quando il codice è in esecuzione in Azure, questo valore sostituisce l'URL localhost contenuto nel file Web.config.

## Test

11. In una finestra del browser passare all'URL della nuova app per le API di livello intermedio appena creata per ToDoListAPI. A questo scopo, fare clic sull'URL nel pannello principale dell'app per le API nel portale.

13. Nella barra degli indirizzi del browser aggiungere "swagger" all'URL e quindi premere INVIO. L'URL è `http://{apiappname}.azurewebsites.net/swagger`.

	Il browser visualizza la stessa interfaccia utente di Swagger vista in precedenza per ToDoListDataAPI, ma `owner` non è un campo obbligatorio per l'operazione Get perché ora è l'app per le API del livello intermedio a inviare tale valore all'app per le API del livello dati. Quando si eseguiranno le esercitazioni sull'autenticazione, il livello intermedio invierà gli ID utente effettivi per il parametro `owner`. Per il momento imposta un asterisco come hardcoded.

12. Provare a usare il metodo Get e gli altri metodi per verificare che l'app per le API di livello intermedio chiami correttamente l'app per le API di livello dati.

	![Metodo Get dell'interfaccia utente di Swagger](./media/app-service-api-dotnet-get-started/midtierget.png)

## Risoluzione dei problemi

In caso di problemi nel corso dell'esercitazione, ecco alcune idee per risolverli:

* Verificare di usare la versione più recente di [Azure SDK per .NET](http://go.microsoft.com/fwlink/?linkid=518003).

* Due dei nomi di progetto sono simili (ToDoListAPI, ToDoListDataAPI). Se l'aspetto degli elementi non è simile a quello descritto nelle istruzioni quando si utilizza un progetto, verificare di avere aperto il progetto corretto.

* Se si ha una rete aziendale e si prova a eseguire la distribuzione nel servizio app di Azure tramite un firewall, assicurarsi che le porte 443 e 8172 siano aperte per la distribuzione Web. Se non è possibile aprire tali porte, si possono usare altri metodi di distribuzione. Vedere [Distribuire l'app nel servizio app di Azure](../app-service-web/web-sites-deploy.md).

* Errori che indicano che i nomi delle route devono essere univoci: è possibile che vengano visualizzati se in un'app per le API è stato accidentalmente distribuito il progetto errato e successivamente viene distribuito quello corretto. Per risolvere il problema, ridistribuire il progetto corretto nell'app per le API e nella scheda **Impostazioni** della procedura guidata **Pubblica sul Web** selezionare **Rimuovi i file aggiuntivi nella destinazione**.

Quando l'app per le API ASP.NET è in esecuzione nel servizio app di Azure, è possibile approfondire la conoscenza delle funzionalità di Visual Studio che semplificano la risoluzione dei problemi. Per informazioni sulla registrazione, il debug remoto e altro ancora, vedere [Risoluzione dei problemi di un'app Web nel servizio app di Azure tramite Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md).

## Passaggi successivi

Si è appreso come distribuire i progetti di API Web esistenti nelle app per le API, generare il codice client per le app per le API e utilizzare le app per le API dai client .NET. L'esercitazione successiva di questa serie mostra come [usare CORS per utilizzare app per le API da client JavaScript](app-service-api-cors-consume-javascript.md).
 
Per altre informazioni sulla generazione di codice client, vedere il repository [Azure/AutoRest](https://github.com/azure/autorest) in GitHub.com. Per informazioni sui problemi relativi all'uso del client generato, aprire un [problema nel repository AutoRest](https://github.com/azure/autorest/issues).

Per creare nuovi progetti di app per le API da zero, usare il modello**App per le API di Azure**.

![Modello di app per le API in Visual Studio](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

Scegliere il modello di progetto **App per le API di Azure** equivale a scegliere il modello di ASP.NET 4.5.2 **Vuoto**, fare clic sulla casella di controllo per aggiungere il supporto per l'API Web e installare il pacchetto NuGet Swashbuckle. Il modello aggiunge anche un codice di configurazione di Swashbuckle progettato per evitare la creazione di ID operazione di Swagger duplicati. Una volta creato un progetto di app per le API, è possibile distribuirlo in un'app per le API nello stesso modo illustrato in questa esercitazione.

<!---HONumber=AcomDC_0824_2016-->