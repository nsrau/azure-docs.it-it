<properties
	pageTitle="Come usare l'SDK del server back-end .NET per App per dispositivi mobili | Servizio app di Azure"
	description="Informazioni su come usare l'SDK del server back-end .NET per App per dispositivi mobili del servizio app di Azure."
	keywords="servizio app, servizio app di Azure, app per dispositivi mobili, servizio mobile, scalabilità, scalabile, distribuzione app, distribuzione app di Azure"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="erikre"
	editor=""/>  

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="glenga"/>

# Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Questo argomento mostra come usare l'SDK del server back-end .NET in scenari chiave di App per dispositivi mobili del servizio app di Azure. Azure Mobile Apps SDK aiuta a lavorare con i client mobili dall'applicazione ASP.NET.

>[AZURE.TIP] L'[SDK del server .NET per App per dispositivi mobili di Azure][2] è open source su GitHub. Il repository contiene tutto il codice sorgente, incluso l'intero gruppo di unit test dell'SDK del server, e alcuni progetti di esempio.

## Documentazione di riferimento

La documentazione di riferimento per l'SDK del server è disponibile qui: [Riferimento .NET di App per dispositivi mobili di Azure][1].

## <a name="create-app"></a>Procedura: Creare un back-end dell'app per dispositivi mobili .NET

Se si inizia un nuovo progetto, è possibile creare un'applicazione del servizio app usando il [portale di Azure] o Visual Studio. È possibile eseguire l'applicazione del servizio app localmente o pubblicare il progetto nell'app per dispositivi mobili del servizio app basata sul cloud.

Se si devono aggiungere funzionalità per dispositivi mobili a un progetto esistente, vedere la sezione [Scaricare e inizializzare l'SDK](#install-sdk).

### Creare un back-end .NET usando il portale di Azure

Per creare un back-end mobile del servizio app, seguire l'[esercitazione introduttiva][3] oppure questi passaggi:

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service-classic](../../includes/app-service-mobile-dotnet-backend-create-new-service-classic.md)]

Nel pannello _Attività iniziali_, in **Creare un'API di tabella** scegliere **C#** come **Linguaggio back-end**. Fare clic su **Download**, estrarre i file di progetto compressi nel computer locale e aprire la soluzione in Visual Studio.

### Creare un back-end .NET usando Visual Studio 2013 e Visual Studio 2015

Per creare un progetto App per dispositivi mobili di Azure in Visual Studio, installare [Azure SDK per .NET][4] (versione 2.9.0 o successiva). Dopo avere installato l'SDK, creare un'applicazione ASP.NET seguendo questa procedura:

1. Aprire la finestra di dialogo **Nuovo progetto** da *File* > **Nuovo** > **Progetto**.
2. Espandere **Modelli** > **Visual C#** e selezionare **Web**.
3. Selezionare **Applicazione Web ASP.NET**.
4. Immettere il nome del progetto. Fare quindi clic su **OK**.
5. In _Modelli ASP.NET 4.5.2_ selezionare **App mobile di Azure**. Selezionare **Host nel cloud** per creare un back-end mobile nel cloud in cui è possibile pubblicare questo progetto.
6. Fare clic su **OK**.

## <a name="install-sdk"></a>Procedura: Scaricare e inizializzare l'SDK

L'SDK è disponibile in [NuGet.org]. Il pacchetto include le funzionalità di base necessarie per iniziare a usare l'SDK. Per inizializzare l'SDK, è necessario eseguire alcune operazioni nell'oggetto **HttpConfiguration**.

### Installare l'SDK

Per installare l'SDK, fare clic con il pulsante destro del mouse sul progetto server in Visual Studio, scegliere **Gestisci pacchetti NuGet**, cercare il pacchetto [Microsoft.Azure.Mobile.Server] e quindi fare clic su **Installa**.

###<a name="server-project-setup"></a> Inizializzare il progetto server

Un progetto server back-end .NET viene inizializzato in modo simile ad altri progetti ASP.NET, includendo una classe di avvio OWIN. Assicurarsi che si sia fatto riferimento al pacchetto NuGet `Microsoft.Owin.Host.SystemWeb`. Per aggiungere questa classe in Visual Studio, fare clic con il pulsante destro del mouse sul progetto server e scegliere **Aggiungi** > **Nuovo elemento**, quindi **Web** > **Generale** > **Classe di avvio OWIN**. Viene generata una classe con l'attributo seguente:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Nel metodo `Configuration()` della classe di avvio OWIN usare un oggetto **HttpConfiguration** per configurare l'ambiente di App per dispositivi mobili di Azure. L'esempio seguente inizializza il progetto server senza funzionalità aggiuntive:

	// in OWIN startup class
	public void Configuration(IAppBuilder app)
	{
	    HttpConfiguration config = new HttpConfiguration();

	    new MobileAppConfiguration()
	        // no added features
	        .ApplyTo(config);

	    app.UseWebApi(config);
	}

Per abilitare le singole funzionalità, è necessario chiamare i metodi di estensione nell'oggetto **MobileAppConfiguration** prima di chiamare **ApplyTo**. Ad esempio, il codice seguente aggiunge le route predefinite a tutti i controller API che hanno l'attributo `[MobileAppController]` durante l'inizializzazione:

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

L'avvio rapido del server nel portale di Azure chiama **UseDefaultConfiguration()**. Questo equivale alla configurazione seguente:

		new MobileAppConfiguration()
			.AddMobileAppHomeController()             // from the Home package
			.MapApiControllers()
			.AddTables(                               // from the Tables package
				new MobileAppTableConfiguration()
					.MapTableControllers()
					.AddEntityFramework()             // from the Entity package
				)
			.AddPushNotifications()                   // from the Notifications package
			.MapLegacyCrossDomainController()         // from the CrossDomain package
			.ApplyTo(config);

I metodi di estensione usati sono:

* `AddMobileAppHomeController()` fornisce la home page di App per dispositivi mobili di Azure predefinita.
* `MapApiControllers()` fornisce le funzionalità API per i controller WebAPI decorati con l'attributo `[MobileAppController]`.
* `AddTables()` fornisce una mapping degli endpoint `/tables` ai controller delle tabelle.
* `AddTablesWithEntityFramework()` è una sintassi abbreviata per il mapping degli endpoint `/tables` con i controller basati su Entity Framework.
* `AddPushNotifications()` fornisce un semplice metodo di registrazione dei dispositivi per Hub di notifica.
* `MapLegacyCrossDomainController()` fornisce intestazioni CORS standard per lo sviluppo locale.

### Estensioni SDK

I pacchetti di estensione basati su NuGet seguenti forniscono diverse funzionalità per dispositivi mobili che l'applicazione può usare. È possibile abilitare le estensioni durante l'inizializzazione usando l'oggetto **MobileAppConfiguration**.

- [Microsoft.Azure.Mobile.Server.Quickstart] Supporta la configurazione di base di app per dispositivi mobili. Viene aggiunta alla configurazione chiamando il metodo di estensione **UseDefaultConfiguration** durante l'inizializzazione. Questa estensione include le estensioni seguenti: pacchetti Notifications, Authentication, Entity, Tables, Crossdomain e Home. Questo pacchetto viene usato da Avvio rapido di App per dispositivi mobili disponibile nel portale di Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Implementa la pagina predefinita *this mobile app is up and running* per la radice del sito Web. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) include le classi per usare i dati e configura la pipeline dei dati. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) consente a Entity Framework di accedere ai dati nel database SQL. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] Consente l'autenticazione e configura il middleware OWIN usato per convalidare i token. Viene aggiunta alla configurazione chiamando i metodi di estensione **AddAppServiceAuthentication** e **IAppBuilder**.**UseAppServiceAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] Consente le notifiche push e definisce un endpoint di registrazione push. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) Crea un controller che fornisce i dati ai Web browser legacy dall'app per dispositivi mobili. Viene aggiunta alla configurazione chiamando il metodo di estensione **MapLegacyCrossDomainController**.

- [Microsoft.Azure.Mobile.Server.Login] offre il metodo AppServiceLoginHandler.CreateToken(), che è un metodo statico usato negli scenari di autenticazione personalizzati.

## <a name="publish-server-project"></a>Procedura: Pubblicare il progetto server

Questa sezione illustra come pubblicare il progetto back-end .NET da Visual Studio. È anche possibile distribuire il progetto back-end usando Git o uno degli altri metodi illustrati nella [documentazione sulla distribuzione del servizio app di Azure](../app-service-web/web-sites-deploy.md).

1. In Visual Studio compilare nuovamente il progetto per ripristinare i pacchetti NuGet.

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**. La prima volta che si crea una pubblicazione, è necessario definire un profilo di pubblicazione. Se si ha già un profilo definito, è possibile selezionarlo e fare clic su **Pubblica**.

2. Se viene richiesto di selezionare una destinazione di pubblicazione, fare clic su **Servizio app di Microsoft Azure** > **Avanti** e quindi, se necessario, accedere con le credenziali di Azure. Visual Studio scaricherà e memorizzerà le impostazioni di pubblicazione direttamente da Azure.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)  

3. Scegliere la **Sottoscrizione**, selezionare **Tipo di risorsa** da **Visualizza**, espandere **App per dispositivi mobili** e fare clic sul back-end di App per dispositivi mobili, quindi su **OK**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)  

4. Verificare le informazioni sul profilo di pubblicazione e fare clic su **Pubblica**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)  

	Quando il back-end dell'app per dispositivi mobili ha eseguito la pubblicazione, viene visualizzata una pagina di destinazione.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)  

##<a name="define-table-controller"></a> Procedura: Definire un controller tabelle

Definire un controller tabelle per esporre una tabella SQL ai client per dispositivi mobili. Per configurare un controller tabelle, sono necessari tre passaggi:

1. Creare una classe di oggetti di trasferimento dei dati.
2. Configurare un riferimento a tabella nella classe DbContext per dispositivi mobili.
3. Creare un controller tabelle.

Un oggetto di trasferimento dei dati è un oggetto C# normale che eredita `EntityData`. Ad esempio:

    public class TodoItem : EntityData
    {
        public string Text { get; set; }
        public bool Complete {get; set;}
    }

L'oggetto di trasferimento dei dati viene usato per definire la tabella nel database SQL. Per creare la voce del database, aggiungere una proprietà `DbSet<>` alla classe DbContext usata. Nel modello di progetto predefinito per App per dispositivi mobili di Azure DbContext è chiamata `Models\MobileServiceContext.cs`:

    public class MobileServiceContext : DbContext
    {
        private const string connectionStringName = "Name=MS_TableConnectionString";

        public MobileServiceContext() : base(connectionStringName)
        {

        }

        public DbSet<TodoItem> TodoItems { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Add(
                new AttributeToColumnAnnotationConvention<TableColumnAttribute, string>(
                    "ServiceColumnTable", (property, attributes) => attributes.Single().ColumnType.ToString()));
        }
    }

Se Azure SDK è installato, ora è possibile creare un controller tabelle per il modello, come segue:

1. Fare clic con il pulsante destro del mouse sulla cartella Controller e quindi scegliere **Aggiungi** > **Controller**.
2. Selezionare l'opzione **Controller tabelle di App mobili di Azure**, quindi fare clic su **Aggiungi**.
3. Nella finestra di dialogo **Aggiungi controller**:
    * Nell'elenco a discesa **Classe modello** selezionare il nuovo oggetto di trasferimento dei dati.
    * Nell'elenco a discesa **DbContext** selezionare la classe DbContext di Servizi mobili.
    * Viene automaticamente creato il nome Controller.
4. Fare clic su **Aggiungi**.

Il progetto server di Avvio rapido contiene un esempio di **TodoItemController** semplice.

### Procedura: Modificare le dimensioni di pagina delle tabelle

Per impostazione predefinita, App mobili di Azure restituisce 50 record per ogni richiesta. Il paging garantisce che il client non occupi il thread dell'interfaccia utente o il server troppo a lungo e quindi un'esperienza utente ottimale. Per modificare le dimensioni di paging delle tabelle, aumentare le "dimensioni della query consentite" lato server e le dimensioni della pagina lato client. Le "dimensioni della query consentite" lato server vengono regolate usando l'attributo `EnableQuery`:

    [EnableQuery(PageSize = 500)]

Verificare che il valore di PageSize sia uguale o maggiore delle dimensioni richieste dal client. Fare riferimento alle procedure specifiche del client per informazioni dettagliate su come modificare le dimensioni di pagina del client.

## Procedura: Definire un controller API personalizzato

Il controller API personalizzato fornisce le funzionalità di base per il back-end dell'app per dispositivi mobili esponendo un endpoint. È possibile registrare un controller API specifico per dispositivi mobili usando l'attributo [MobileAppController]. L'attributo `MobileAppController`registra la route, configura il serializzatore JSON delle app per dispositivi mobili e attiva i [controlli di versione client](app-service-mobile-client-and-server-versioning.md).

1. In Visual Studio fare clic con il pulsante destro del mouse sulla cartella Controller e quindi scegliere **Aggiungi** > **Controller**, selezionare **Controller API Web 2&mdash;Vuoto** e fare clic su **Aggiungi**.

2. Specificare un nome in **Nome controller**, ad esempio `CustomController`, e fare clic su **Aggiungi**.

3. Nella nuovo file della classe controller aggiungere l'istruzione using seguente:

		using Microsoft.Azure.Mobile.Server.Config;

4. Applicare l'attributo **[MobileAppController]** alla definizione di classe controller API, come nell'esempio seguente:

		[MobileAppController]
		public class CustomController : ApiController
		{
		      //...
		}

4. Nel file App\_Start/Startup.MobileApp.cs aggiungere una chiamata al metodo di estensione **MapApiControllers**, come nell'esempio seguente:

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);

È anche possibile usare il metodo di estensione `UseDefaultConfiguration()` invece di `MapApiControllers()`. I controller a cui non è applicato **MobileAppControllerAttribute** continuano a essere accessibili da parte dei client, ma potrebbero non essere usati correttamente dai client con l'SDK client di App per dispositivi mobili.

## Procedura: Usare l'autenticazione

App per dispositivi mobili di Azure usa l'autenticazione/autorizzazione del servizio app per proteggere il back-end mobile. Questa sezione illustra come eseguire le attività seguenti relative all'autenticazione nel progetto server back-end .NET:

+ [Procedura: Aggiungere l'autenticazione a un progetto server](#add-auth)
+ [Procedura: Usare l'autenticazione personalizzata per la propria applicazione](#custom-auth)
+ [Procedura: Recuperare le informazioni sull'utente autenticato](#user-info)
+ [Procedura: Limitare l’accesso ai dati per gli utenti autorizzati](#authorize)

### <a name="add-auth"></a>Procedura: Aggiungere l'autenticazione a un progetto server

È possibile aggiungere l'autenticazione al progetto server estendendo l'oggetto **MobileAppConfiguration** e configurando il middleware OWIN. Quando si installa il pacchetto [Microsoft.Azure.Mobile.Server.Quickstart] e si chiama il metodo di estensione **UseDefaultConfiguration**, è possibile andare al passaggio 3.

1. In Visual Studio installare il pacchetto [Microsoft.Azure.Mobile.Server.Authentication].

2. Nel file di progetto Startup.cs aggiungere la riga di codice seguente all'inizio del metodo **Configuration**:

		app.UseAppServiceAuthentication(config);

	Questo componente del middleware OWIN convalida i token rilasciati dal gateway del servizio app associato.

3. Aggiungere l'attributo `[Authorize]` a qualsiasi controller o metodo che richiede l'autenticazione.

Per informazioni su come autenticare i client nel back-end di App per dispositivi mobili, vedere l'articolo [Aggiungere l'autenticazione all'app](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Procedura: Usare l'autenticazione personalizzata per la propria applicazione

Se non si vuole usare uno dei provider di autenticazione/autorizzazione del servizio app, è possibile implementare il proprio sistema di accesso. Installare il pacchetto [Microsoft.Azure.Mobile.Server.Login] per facilitare la generazione dei token di autenticazione. Fornire il proprio codice per la convalida delle credenziali utente. È possibile, ad esempio, confrontare le password con salting e hashing in un database. Nell'esempio seguente il metodo `isValidAssertion()` (definito altrove) è responsabile di questi controlli.

L'autenticazione personalizzata viene esposta creando un ApiController ed esponendo le azioni `register` e `login`. Il client dovrà usare un'interfaccia utente personalizzata per raccogliere le informazioni dall'utente. Le informazioni vengono quindi inviate all'API con una chiamata HTTP POST standard. Dopo la convalida dell'asserzione da parte del server, viene rilasciato un token con il metodo `AppServiceLoginHandler.CreateToken()`. ApiController **non dovrà** usare l'attributo `[MobileAppController]`.

Azione `login` di esempio:

		public IHttpActionResult Post([FromBody] JObject assertion)
		{
			if (isValidAssertion(assertion)) // user-defined function, checks against a database
			{
				JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
					mySigningKey,
					myAppURL,
					myAppURL,
					TimeSpan.FromHours(24) );
				return Ok(new LoginResult()
				{
					AuthenticationToken = token.RawData,
					User = new LoginResultUser() { UserId = userName.ToString() }
				});
			}
			else // user assertion was not valid
			{
				return this.Request.CreateUnauthorizedResponse();
			}
		}

Nell'esempio precedente LoginResult e LoginResultUser sono oggetti serializzabili che espongono le proprietà necessarie. Il client si aspetta che le risposte di accesso vengano restituite come oggetti JSON nel formato:

		{
			"authenticationToken": "<token>",
			"user": {
				"userId": "<userId>"
			}
		}

Il metodo `AppServiceLoginHandler.CreateToken()` include un parametro _audience_ e un parametro _issuer_. Entrambi i parametri vengono impostati sull'URL della radice dell'applicazione, usando lo schema HTTPS. Allo stesso modo, è consigliabile impostare _secretKey_ come valore della chiave per la firma dell'applicazione. Non distribuire la chiave di firma in un client perché può essere usata per creare chiavi e rappresentare utenti. È possibile ottenere la chiave di firma mentre è ospitata nel servizio app facendo riferimento alla variabile di ambiente _WEBSITE\_AUTH\_SIGNING\_KEY_. Se necessario in un contesto di debug locale, seguire le istruzioni nella sezione [Debug locale con autenticazione](#local-debug) per recuperare la chiave e archiviarla come impostazione dell'applicazione.

Il token rilasciato può anche includere altre attestazioni e una data di scadenza. Il token rilasciato deve includere almeno un'attestazione soggetto (**sub**).

È possibile supportare il metodo `loginAsync()` client standard tramite l'overload della route di autenticazione. Se il client chiama `client.loginAsync('custom');` per eseguire l'accesso, la route deve essere `/.auth/login/custom`. È possibile impostare la route per il controller di autenticazione personalizzata usando `MapHttpRoute()`:

    config.Routes.MapHttpRoute("custom", ".auth/login/custom", new { controller = "CustomAuth" });

>[AZURE.TIP] Usare l'approccio `loginAsync()` assicura che il token di autenticazione sia collegato a ogni chiamata successiva al servizio.

###<a name="user-info"></a>Procedura: Recuperare le informazioni sull'utente autenticato

Quando un utente viene autenticato dal servizio app, è possibile accedere all'ID utente assegnato e ad altre informazioni nel codice di back-end .NET. Le informazioni utente possono essere usate per prendere decisioni relative all'autorizzazione nel back-end. Il codice seguente ottiene l'ID utente associato a una richiesta:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Il SID è derivato dall'ID utente specifico del provider ed è statico per un determinato utente e provider di accesso. Il SID è null per i token di autenticazione non validi.

Il servizio app consente anche di richiedere le attestazioni specifiche dal provider di accesso. Ogni provider di identità può fornire altre informazioni usando l'SDK del provider di identità. È possibile, ad esempio, usare l'API Graph Facebook per informazioni sugli amici. È possibile specificare le attestazioni richieste nel pannello del provider nel portale di Azure. Alcune attestazioni richiedono una configurazione aggiuntiva con il provider di identità.

Il codice seguente chiama il metodo di estensione **GetAppServiceIdentityAsync** per ottenere le credenziali di accesso, che includono il token di accesso necessario per effettuare richieste nell'API Graph di Facebook:

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Per fornire il metodo di estensione **GetAppServiceIdentityAsync**, aggiungere un'istruzione using per `System.Security.Principal`.

### <a name="authorize"></a>Procedura: Limitare l'accesso ai dati per gli utenti autorizzati

Nella sezione precedente, è stato illustrato come recuperare l'ID utente di un utente autenticato. È possibile limitare l'accesso ai dati e ad altre risorse in base a questo valore. Ad esempio, l'aggiunta di una colonna userId alle tabelle e l'applicazione di filtri ai risultati delle query in base all'ID utente sono modi semplici per limitare i dati restituiti solo agli utenti autorizzati. Il codice seguente restituisce righe di dati solo quando il SID corrisponde al valore nella colonna UserId nella tabella TodoItem:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

    // Only return data rows that belong to the current user.
    return Query().Where(t => t.UserId == sid);

Il metodo `Query()` restituisce `IQueryable` che può essere modificato da LINQ per gestire i filtri.

## Procedura: Aggiungere notifiche push a un progetto server

Aggiungere notifiche push al progetto server estendendo l'oggetto **MobileAppConfiguration** e creando un client di Hub di notifica.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server, quindi scegliere **Gestisci pacchetti NuGet**, cercare `Microsoft.Azure.Mobile.Server.Notifications` e infine fare clic su **Installa**.

2. Ripetere questo passaggio per installare il pacchetto `Microsoft.Azure.NotificationHubs`, che include la libreria client di Hub di notifica.

3. In App\_Start/Startup.MobileApp.cs aggiungere una chiamata al metodo di estensione **AddPushNotifications()** durante l'inizializzazione:

		new MobileAppConfiguration()
			// other features...
			.AddPushNotifications()
			.ApplyTo(config);

4. Aggiungere il codice seguente per creare un client di Hub di notifica:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

Ora è possibile usare il client di Hub di notifica per inviare notifiche push ai dispositivi registrati. Per altre informazioni, vedere l'articolo relativo all'[aggiunta di notifiche push all'app](app-service-mobile-ios-get-started-push.md) Per altre informazioni su Hub di notifica, vedere [Panoramica di Hub di notifica](../notification-hubs/notification-hubs-push-notification-overview.md).

##<a name="tags"></a>Procedura: Abilitare le notifiche push mirate usando i tag

Hub di notifica consente di inviare notifiche mirate a registrazioni specifiche tramite tag. Diversi tag vengono creati automaticamente:

* L'ID installazione identifica un dispositivo specifico.
* L'ID utente basato sul SID autenticato identifica un utente specifico.

È possibile accedere all'ID di installazione dalla proprietà **installationId** in **MobileServiceClient**. L'esempio seguente illustra come usare un ID di installazione per aggiungere un tag a una specifica installazione di Hub di notifica:

	hub.PatchInstallation("my-installation-id", new[]
	{
	    new PartialUpdateOperation
	    {
	        Operation = UpdateOperationType.Add,
	        Path = "/tags",
	        Value = "{my-tag}"
	    }
	});

I tag forniti dal client durante la registrazione per le notifiche push vengono ignorati dal back-end durante la creazione dell'installazione. Per consentire a un client di aggiungere tag all'installazione, è necessario creare un'API personalizzata che aggiunge tag usando il modello precedente.

Per un esempio, vedere [Client-added push notification tags][5] (Tag di notifica push aggiunti dal client) nell'esempio di avvio rapido completato di app per dispositivi mobili del servizio app.

##<a name="push-user"></a>Procedura: Inviare notifiche push agli utenti autenticati

Se un utente autenticato esegue la registrazione per le notifiche push, viene automaticamente aggiunto un tag con l'ID utente. Usando questo tag, è possibile inviare notifiche push a tutti i dispositivi registrati da tale persona. Il codice seguente ottiene il SID dell'utente che esegue la richiesta e invia un modello di notifica push a ogni registrazione del dispositivo per tale persona:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Durante la registrazione per le notifiche push da un client autenticato, assicurarsi che l'autenticazione sia stata completata prima di tentare la registrazione. Per altre informazioni, vedere [Push to users][6] (Eseguire il push agli utenti) nell'esempio di avvio rapido completato per le app per dispositivi mobili del servizio app per back-end .NET.

## Procedura: Eseguire il debug e risolvere i problemi di .NET Server SDK

Il servizio app di Azure offre diverse tecniche di debug e risoluzione dei problemi per le applicazioni ASP.NET:

- [Monitoraggio di un servizio app di Azure](../app-service-web/web-sites-monitor.md)
- [Abilitazione della registrazione diagnostica nel servizio app di Azure](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Risoluzione dei problemi di un Servizio app di Azure in Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### Registrazione

È possibile scrivere nei log di diagnostica del servizio app usando la scrittura di tracce ASP.NET standard: Prima di poter scrivere nei log, è necessario abilitare la diagnostica nel back-end di App per dispositivi mobili.

Per abilitare la diagnostica e scrivere nei log:

1. Seguire i passaggi in [Come abilitare la diagnostica](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag).

2. Aggiungere l'istruzione using seguente al file del codice:

		using System.Web.Http.Tracing;

3. Creare un writer di traccia per scrivere dal back-end .NET nei log di diagnostica, come indicato di seguito:

		ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
		traceWriter.Info("Hello, World");

4. Ripubblicare il progetto server e accedere al back-end di App per dispositivi mobili per eseguire il percorso del codice con la registrazione.

5. Scaricare e valutare i log, come descritto in [Procedura: Scaricare i log](../app-service-web/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Debug locale con autenticazione

È possibile eseguire l'applicazione in locale per testare le modifiche prima di pubblicarle nel cloud. Per la maggior parte dei back-end di App per dispositivi mobili di Azure, premere *F5* in Visual Studio. Esistono però alcune considerazioni aggiuntive quando si usa l'autenticazione.

È necessaria un'app per dispositivi mobili basata sul cloud con l'autenticazione/autorizzazione del servizio app configurata e il client deve avere l'endpoint cloud specificato come host di accesso alternativo. Per i passaggi specifici da eseguire, vedere la documentazione della piattaforma client.

Verificare che nel back-end mobile sia installato [Microsoft.Azure.Mobile.Server.Authentication]. Quindi nella classe di avvio OWIN dell'applicazione aggiungere quanto segue, dopo che `MobileAppConfiguration` è stato applicato a `HttpConfiguration`:

		app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
		{
			SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
			ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
			ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
			TokenHandler = config.GetAppServiceTokenHandler()
		});

Nell'esempio precedente è consigliabile configurare le impostazioni dell'applicazione _authAudience_ e _authIssuer_ nel file Web.config in modo che ognuna sia l'URL della radice dell'applicazione, usando lo schema HTTPS. Allo stesso modo, è consigliabile impostare _authSigningKey_ come valore della chiave per la firma dell'applicazione. Per ottenere la chiave di firma:

1. Andare all'app nel [portale di Azure].
2. Fare clic su **Strumenti**, **Kudu**, **Vai**.
3. Nel sito di gestione di Kudu fare clic su **Environment** (Ambiente).
4. Trovare il valore di _WEBSITE\_AUTH\_SIGNING\_KEY_.

Usare la chiave di firma per il parametro _authSigningKey_ nel file di configurazione dell'applicazione locale. Il back-end mobile, quando è in esecuzione in locale, ora riesce a convalidare i token che il client ottiene dall'endpoint basato sul cloud.

[1]: https://msdn.microsoft.com/library/azure/dn961176.aspx
[2]: https://github.com/Azure/azure-mobile-apps-net-server
[3]: app-service-mobile-ios-get-started.md
[4]: https://azure.microsoft.com/downloads/
[5]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags
[6]: https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users
[portale di Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/
[MapHttpAttributeRoutes]: https://msdn.microsoft.com/library/dn479134(v=vs.118).aspx

<!---HONumber=AcomDC_0921_2016-->