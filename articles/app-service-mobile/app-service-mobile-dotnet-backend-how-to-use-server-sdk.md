<properties
	pageTitle="Come usare l'SDK del server back-end .NET per App per dispositivi mobili | Servizio app di Azure"
	description="Informazioni su come usare l'SDK del server back-end .NET per App per dispositivi mobili del servizio app di Azure."
	keywords="servizio app, servizio app di Azure, app per dispositivi mobili, servizio mobile, scalabilità, scalabile, distribuzione app, distribuzione app di Azure"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="glenga"/>

# Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Questo argomento mostra come usare l'SDK del server back-end .NET in scenari chiave di App per dispositivi mobili del servizio app di Azure. Azure Mobile Apps SDK aiuta a lavorare con i client mobili dall'applicazione ASP.NET.

>[AZURE.TIP] L'[SDK del server .NET per App per dispositivi mobili di Azure](https://github.com/Azure/azure-mobile-apps-net-server) è open source su GitHub. Il repository contiene l’intero gruppo di test dell’unità SDK del server, nonché alcuni progetti di esempio.

## Documentazione di riferimento

La documentazione di riferimento per l'SDK del server è disponibile qui: [Riferimento .NET di App per dispositivi mobili di Azure](https://msdn.microsoft.com/library/azure/dn961176.aspx).

## <a name="create-app"></a>Procedura: creare un backend .NET per l'applicazione per dispositivi mobili

Se si inizia un nuovo progetto, è possibile creare un'applicazione del servizio app usando il [portale di Azure] o Visual Studio. Questa sezione illustra come usare uno dei due per creare il back-end per una nuova applicazione per dispositivi mobili che ospita l'API per un semplice elenco attività. È possibile eseguirlo localmente o pubblicare il progetto nell'app per dispositivi mobili del servizio app basata sul cloud.

Se si devono aggiungere funzionalità per dispositivi mobili a un progetto esistente, vedere più avanti la sezione [Scaricare e inizializzare l'SDK](#install-sdk).

### Creare un back-end .NET usando il portale di Azure

È possibile creare una nuova applicazione per dispositivi mobili direttamente nel [portale di Azure]. È possibile seguire questi passaggi o creare un nuovo client e un nuovo server seguendo l'esercitazione [Creare un'app per dispositivi mobili](app-service-mobile-ios-get-started.md).

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

&nbsp;&nbsp;9. Nel pannello _Attività iniziali_, in **Creare un'API di tabella** scegliere **C#** come **Linguaggio back-end**.

&nbsp;&nbsp;10. Fare clic su Download, estrarre i file compressi del progetto nel computer locale e aprire la soluzione in Visual Studio.

### Creare un back-end .NET usando Visual Studio 2013 e Visual Studio 2015

Per creare un progetto App per dispositivi mobili in Visual Studio, sarà necessario installare [Azure SDK per .NET](https://azure.microsoft.com/downloads/), versione 2.8.1 o successiva. Una volta installato l'SDK, creare una nuova applicazione ASP.NET:

1. Aprire la finestra di dialogo **Nuovo progetto** da *File* > **Nuovo** > **Progetto**.

2. Espandere **Modelli** > **Visual C#** e selezionare **Web**.

3. Selezionare **Applicazione Web ASP.NET**.

4. Immettere il nome del progetto. Fare quindi clic su **OK**.

5. In _Modelli ASP.NET 4.5.2_ selezionare **App mobile di Azure**. Selezionare **Host nel cloud** per creare una nuova app per dispositivi mobili nel cloud in cui è possibile pubblicare questo progetto.

6. Fare clic su **OK**. L'applicazione verrà creata e visualizzata in Esplora soluzioni.

## <a name="install-sdk"></a>Procedura: Scaricare e inizializzare l'SDK

L'SDK è disponibile in [NuGet.org]. Il pacchetto include le funzionalità di base necessarie per iniziare a usare l'SDK. Per inizializzare l'SDK, è necessario eseguire alcune operazioni nell'oggetto **HttpConfiguration**.

###Installare l'SDK

Per installare l'SDK, fare clic con il pulsante destro del mouse sul progetto server in Visual Studio, scegliere **Gestisci pacchetti NuGet**, cercare il pacchetto [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) e quindi fare clic su **Installa**.

###<a name="server-project-setup"></a> Inizializzare il progetto server

Un progetto server back-end .NET viene inizializzato in modo simile ad altri progetti ASP.NET, includendo una classe di avvio OWIN. Assicurarsi che si sia fatto riferimento al pacchetto NuGet `Microsoft.Owin.Host.SystemWeb`. Per aggiungere questa classe in Visual Studio, fare clic con il pulsante destro del mouse sul progetto server e scegliere **Aggiungi** > **Nuovo elemento**, quindi **Web** > **Generale** > **Classe di avvio OWIN**.

Verrà generata una classe con l'attributo seguente:

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Nel metodo `Configuration()` della classe di avvio OWIN configurare il progetto server usando un oggetto **HttpConfiguration** che rappresenta le opzioni di configurazione per il servizio. L'esempio seguente inizializza il progetto server, senza funzionalità aggiuntive:

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

Si noti che `MapApiControllers` esegue solo il mapping dei controller con l'attributo `[MobileAppController]`.

Molti metodi di estensione delle funzionalità sono disponibili tramite pacchetti NuGet aggiuntivi che è possibile includere, descritti nella sezione seguente.

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


### Estensioni SDK

I pacchetti di estensione basati su NuGet seguenti forniscono diverse funzionalità per dispositivi mobili che l'applicazione può usare. È possibile abilitare le estensioni durante l'inizializzazione usando l'oggetto **MobileAppConfiguration**.

- [Microsoft.Azure.Mobile.Server.Quickstart] Supporta la configurazione di base di app per dispositivi mobili. Viene aggiunta alla configurazione chiamando il metodo di estensione **UseDefaultConfiguration** durante l'inizializzazione. Questa estensione include le estensioni seguenti: pacchetti Notifications, Authentication, Entity, Tables, Crossdomain e Home. È equivalente al progetto server di avvio rapido che è possibile scaricare dal portale di Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Implementa la pagina predefinita *this mobile app is up and running* per la radice del sito Web. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) include le classi per usare i dati e configura la pipeline dei dati. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) consente a Entity Framework di accedere ai dati nel database SQL. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] Consente l'autenticazione e configura il middleware OWIN usato per convalidare i token. Viene aggiunta alla configurazione chiamando i metodi di estensione **AddAppServiceAuthentication** e **IAppBuilder**.**UseAppServiceAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] Consente le notifiche push e definisce un endpoint di registrazione push. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) Crea un controller che fornisce i dati ai Web browser legacy dall'app per dispositivi mobili. Viene aggiunta alla configurazione chiamando il metodo di estensione **MapLegacyCrossDomainController**.

- [Microsoft.Azure.Mobile.Server.Login] Offre il supporto per l'anteprima per l'autenticazione personalizzata con il metodo AppServiceLoginHandler.CreateToken(). Si tratta di un metodo statico che non è necessario abilitare nella configurazione.

## <a name="publish-server-project"></a>Procedura: Pubblicare il progetto server

Questa sezione illustra come pubblicare il progetto back-end .NET da Visual Studio. È anche possibile distribuire il progetto back-end usando Git o uno degli altri metodi illustrati nella [documentazione sulla distribuzione del servizio app di Azure](../app-service-web/web-sites-deploy.md).

1. In Visual Studio compilare nuovamente il progetto per ripristinare i pacchetti NuGet.

2. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, quindi scegliere **Pubblica**. La prima volta che si pubblica è necessario definire un profilo di pubblicazione. Se si ha già un profilo definito, è possibile semplicemente selezionarlo e fare clic su **Pubblica**.

2. Se viene richiesto di selezionare una destinazione di pubblicazione, fare clic su **Servizio app di Microsoft Azure** > **Avanti** e quindi, se necessario, accedere con le credenziali di Azure. Visual Studio scaricherà e memorizzerà le impostazioni di pubblicazione direttamente da Azure.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. Scegliere la **Sottoscrizione**, selezionare **Tipo di risorsa** da **Visualizza**, espandere **App per dispositivi mobili** e fare clic sul back-end di App per dispositivi mobili, quindi su **OK**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. Verificare le informazioni sul profilo di pubblicazione e fare clic su **Pubblica**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

	Quando il back-end dell'app per dispositivi mobili ha eseguito la pubblicazione, viene visualizzata una pagina di destinazione.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

## Procedura: Definire un controller tabelle

Un controller tabelle fornisce l'accesso ai dati delle entità in un archivio dati basato su tabelle, ad esempio il database SQL o l'Archivio tabelle di Azure. I controller della tabella ereditano dalla classe generica **TableController**, dove il tipo generico è un'entità nel modello che rappresenta lo schema della tabella, come indicato di seguito:

	public class TodoItemController : TableController<TodoItem>
    {
		//...
	}

I controller della tabella vengono inizializzati usando il metodo di estensione **AddTables**. Ciò consente di aggiungere route in `/tables/` per tutte le sottoclassi di `TableController`.

L'esempio seguente inizializza un controller tabelle che usa Entity Framework per accedere ai dati:

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);

Per un esempio di un controller tabelle che usa Entity Framework per accedere ai dati da un database SQL di Azure, vedere la classe **TodoItemController** nel progetto server di avvio rapido che è possibile scaricare dal portale di Azure.

## Procedura: Definire un controller API personalizzato

Il controller API personalizzato fornisce le funzionalità di base per il back-end dell'app per dispositivi mobili esponendo un endpoint. È possibile registrare un controller API specifico per dispositivi mobili usando l'attributo [MobileAppController]. Questo attributo registra la route e configura anche il serializzatore JSON delle app per dispositivi mobili.

1. In Visual Studio fare clic con il pulsante destro del mouse sulla cartella Controller e quindi scegliere **Aggiungi** > **Controller**, selezionare **Controller API Web 2&mdash;Vuoto** e fare clic su **Aggiungi**.

2. Specificare un nome in **Nome controller**, ad esempio `CustomController`, e fare clic su **Aggiungi**. Verrà creata una nuova classe **CustomController** che eredita da **ApiController**.

3. Nella nuovo file della classe controller aggiungere l'istruzione using seguente:

		using Microsoft.Azure.Mobile.Server.Config;

4. Applicare l'attributo **[MobileAppController]** alla definizione della classe controller API, come nell'esempio seguente:

		[MobileAppController]
		public class CustomController : ApiController
		{
		      //...
		}

4. Nel file App\_Start/Startup.MobileApp.cs aggiungere una chiamata al metodo di estensione **MapApiControllers**, come nell'esempio seguente:

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);

	Si noti che non è necessario chiamare **MapApiControllers** quando si chiama **UseDefaultConfiguration**, che inizializza tutte le funzionalità.

I controller a cui non è applicato **MobileAppControllerAttribute** continuano a essere accessibili da parte dei client, ma potrebbero non essere usati correttamente dai client con l'SDK client di App per dispositivi mobili.

## Procedura: Usare l'autenticazione

App per dispositivi mobili usa le funzionalità di autenticazione del servizio app e di ASP.NET per semplificare il processo di abilitazione dell'autenticazione per le proprie app. Questa sezione illustra come eseguire le attività seguenti relative all'autenticazione nel progetto server back-end .NET:

+ [Procedura: Aggiungere l'autenticazione a un progetto server](#add-auth)
+ [Procedura: Usare l'autenticazione personalizzata per la propria applicazione](#custom-auth)
+ [Procedura: Recuperare le informazioni sull'utente autenticato](#user-info)

### <a name="add-auth"></a>Procedura: Aggiungere l'autenticazione a un progetto server

È possibile aggiungere l'autenticazione al progetto server estendendo l'oggetto **MobileAppConfiguration** e configurando il middleware OWIN. Quando si installa il pacchetto [Microsoft.Azure.Mobile.Server.Quickstart] e si chiama il metodo di estensione **UseDefaultConfiguration**, è possibile andare al passaggio 3.

1. In Visual Studio installare il pacchetto [Microsoft.Azure.Mobile.Server.Authentication].

2. Nel file di progetto Startup.cs aggiungere la riga di codice seguente all'inizio del metodo **Configuration**:

		app.UseAppServiceAuthentication(config);

	In questo modo viene aggiunto il componente middleware OWIN, che consente all'app per dispositivi mobili di Azure di convalidare i token rilasciati dal gateway del servizio app associato.

3. Aggiungere l'attributo `[Authorize]` a qualsiasi controller o metodo che richiede l'autenticazione. Gli utenti devono ora essere autenticati per accedere a tale endpoint o ad API specifiche.

Per informazioni su come autenticare i client nel back-end di App per dispositivi mobili, vedere l'articolo [Aggiungere l'autenticazione all'app](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Procedura: Usare l'autenticazione personalizzata per la propria applicazione

È possibile scegliere di fornire il proprio sistema di accesso se non si vuole usare uno dei provider di autenticazione/autorizzazione del servizio app. A questo scopo, installare il pacchetto [Microsoft.Azure.Mobile.Server.Login].

Sarà necessario fornire la propria logica per determinare se un utente deve essere registrato. È possibile, ad esempio, confrontare le password con salting e hashing in un database. Nell'esempio seguente il metodo `isValidAssertion()` è responsabile di questi controlli e viene definito altrove.

L'autenticazione personalizzata viene esposta creando un nuovo ApiController ed esponendo le azioni di registrazione e accesso come quella seguente. Il client può tentare l'accesso raccogliendo le informazioni pertinenti dall'utente e inviando un POST HTTPS all'API con le informazioni utente nel corpo. Dopo la convalida dell'asserzione da parte del server, è possibile rilasciare un token con il metodo `AppServiceLoginHandler.CreateToken()`.

Ecco un esempio di azione di accesso:

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

Nel codice precedente LoginResult e LoginResultUser sono solo semplici oggetti che espongono le proprietà visualizzate. Il client si aspetta che le risposte di accesso tornino come oggetti JSON nel formato:

		{
			"authenticationToken": "<token>",
			"user": {
				"userId": "<userId>"
			}
		}

Il metodo `MobileAppLoginHAppServiceLoginHandlerandler.CreateToken()` include un parametro _audience_ e un parametro _issuer_. Entrambi vengono in genere impostati sull'URL della radice dell'applicazione, usando lo schema HTTPS Allo stesso modo, è consigliabile impostare _secretKey_ come valore della chiave per la firma dell'applicazione. Si tratta di un valore sensibile che non deve mai essere condiviso o incluso in un client. È possibile ottenere questo valore mentre è ospitato nel servizio app facendo riferimento alla variabile di ambiente _WEBSITE\_AUTH\_SIGNING\_KEY_. Se necessario in un contesto di debug locale, seguire le istruzioni nella sezione [Debug locale con autenticazione](#local-debug) per recuperare la chiave e archiviarla come impostazione dell'applicazione.

È anche necessario fornire una durata per il token rilasciato, oltre a eventuali attestazioni che si vuole includere. È necessario fornire un'attestazione dell'oggetto, come mostrato nel codice di esempio.

È anche possibile semplificare il codice client per usare il metodo `loginAsync()`, il cui nome può variare a seconda della piattaforma, anziché un HTTP POST manuale. Verrà usato l'overload che accetta un parametro token aggiuntivo e che è correlato all'oggetto di asserzione della richiesta POST. Il provider in questo caso deve essere un nome personalizzato a scelta. Nel server l'azione di accesso deve avvenire nel percorso _/.auth/login/{nomeProviderPersonalizzato}_ che include il nome personalizzato. Per inserire il controller in questo percorso, aggiungere una route a HttpConfiguration prima di applicare MobileAppConfiguration.

		config.Routes.MapHttpRoute("CustomAuth", ".auth/login/CustomAuth", new { controller = "CustomAuth" });

Sostituire la stringa "CustomAuth" riportata in precedenza con il nome del controller che ospita l'azione di accesso.

>[AZURE.TIP] Usare l'approccio loginAsync() assicura che il token di autenticazione sia collegato a ogni chiamata successiva al servizio.

###<a name="user-info"></a>Procedura: Recuperare le informazioni sull'utente autenticato

Quando un utente viene autenticato dal servizio app, è possibile accedere all'ID utente assegnato e ad altre informazioni nel codice di back-end .NET. Questa opzione è utile per prendere decisioni relative alle autorizzazioni per un determinato utente nel back-end, ad esempio per decidere se un utente può accedere a una riga di tabella o a un'altra risorsa. Il codice seguente illustra come ottenere l'ID utente per un utente connesso:

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Il SID è derivato dall'ID utente specifico del provider ed è statico per un determinato utente e provider di accesso.

Il servizio app consente anche di richiedere le attestazioni specifiche dal provider di accesso. Ciò consente di richiedere altre informazioni dal provider, usando ad esempio le API Graph di Facebook. È possibile specificare le attestazioni nel pannello del provider nel portale. Alcune attestazioni richiedono una configurazione aggiuntiva con il provider.

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

Si noti che affinché il metodo di estensione **GetAppServiceIdentityAsync** funzioni, è necessario aggiungere un'istruzione using per `System.Security.Principal`.


## Procedura: Aggiungere notifiche push a un progetto server

È possibile aggiungere notifiche push al progetto server estendendo l'oggetto **MobileAppConfiguration** e creando un client di Hub di notifica. Quando si installa il pacchetto [Microsoft.Azure.Mobile.Server.Quickstart] e si chiama il metodo di estensione **UseDefaultConfiguration**, è possibile andare al passaggio 3.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server, scegliere **Gestisci pacchetti NuGet**, cercare Microsoft.Azure.Mobile.Server.Notifications e quindi fare clic su **Installa**. In questo modo viene installato il pacchetto [Microsoft.Azure.Mobile.Server.Notifications].

3. Ripetere questo passaggio per installare il pacchetto `Microsoft.Azure.NotificationHubs`, che include la libreria client di Hub di notifica.

2. In App\_Start/Startup.MobileApp.cs aggiungere una chiamata al metodo di estensione **AddPushNotifications** durante l'inizializzazione, come nell'esempio seguente:

		new MobileAppConfiguration()
			// other features...
			.AddPushNotifications()
			.ApplyTo(config);

	In questo modo viene creato l'endpoint di registrazione delle notifiche push nel progetto server. Questo endpoint viene usato dai client per la registrazione con l'hub di notifica associato. A questo punto è necessario aggiungere il client di Hub di notifica usato per inviare le notifiche.

3. In un controller da cui si vogliono inviare le notifiche push aggiungere l'istruzione using seguente:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;

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

A questo punto, è possibile usare il client di Hub di notifica per inviare notifiche push ai dispositivi registrati. Per altre informazioni, vedere l'articolo relativo all'[aggiunta di notifiche push all'app](app-service-mobile-ios-get-started-push.md) Per altre informazioni su tutte le operazioni disponibili con Hub di notifica, vedere [Panoramica dell'Hub di notifica di Azure](../notification-hubs/notification-hubs-overview.md).

##<a name="tags"></a>Procedura: Aggiungere tag all'installazione di un dispositivo per abilitare l'invio di notifiche push mirate

Hub di notifica consente di inviare notifiche mirate a registrazioni specifiche tramite tag. Un tag creato automaticamente è l'ID di installazione, che è specifico di un'istanza dell'app su un determinato dispositivo. Una registrazione con un ID di installazione viene definita anche *installazione*. È possibile usare l'ID di installazione per gestire l'installazione, ad esempio per l'aggiunta di tag. È possibile accedere all'ID di installazione dalla proprietà **installationId** in **MobileServiceClient**.

L'esempio seguente illustra come usare un ID di installazione per aggiungere un tag a una specifica installazione di Hub di notifica:

	hub.PatchInstallation("my-installation-id", new[]
	{
	    new PartialUpdateOperation
	    {
	        Operation = UpdateOperationType.Add,
	        Path = "/tags",
	        Value = "{my-tag}"
	    }
	});

Si noti che i tag forniti dal client durante la registrazione per le notifiche push vengono ignorati dal back-end durante la creazione dell'installazione. Per consentire a un client di aggiungere tag all'installazione, è necessario creare una nuova API personalizzata che aggiunge tag tramite il modello precedente. Per un esempio di un controller di API personalizzato che consente ai client di aggiungere tag a un'installazione, vedere l'articolo relativo ai [tag di notifica push aggiunti dal client](https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags) nell'esempio di avvio rapido completato in app per dispositivi mobili del servizio app per back-end .NET.

##<a name="push-user"></a>Procedura: Inviare notifiche push agli utenti autenticati

Se un utente autenticato esegue la registrazione per le notifiche push, viene automaticamente aggiunto un tag con l'ID utente. Tramite questo tag, è possibile inviare notifiche push a tutti i dispositivi registrati da un utente specifico. Il codice seguente ottiene il SID dell'utente che esegue la richiesta e invia un modello di notifica push a ogni registrazione del dispositivo per tale utente:

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);
    
Durante la registrazione per le notifiche push da un client autenticato, assicurarsi che l'autenticazione sia stata completata prima di tentare la registrazione. Per altre informazioni, vedere [Eseguire il push agli utenti](https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users) nell'esempio di avvio rapido completato in app per dispositivi mobili del servizio app per back-end .NET.

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

È possibile eseguire l'applicazione in locale per testare le modifiche prima di pubblicarle nel cloud. Per molte app, è sufficiente premere *F5* in Visual Studio. Esistono però alcune considerazioni aggiuntive quando si usa l'autenticazione.

È necessaria un'app per dispositivi mobili basata sul cloud con l'autenticazione/autorizzazione del servizio app configurata e il client deve avere l'endpoint cloud specificato come host di accesso alternativo. Per i passaggi specifici da eseguire, vedere la documentazione della piattaforma client scelta ([iOS](app-service-mobile-ios-how-to-use-client-library.md), [Windows/Xamarin](app-service-mobile-dotnet-how-to-use-client-library.md)).

Verificare che nell'applicazione sia installato [Microsoft.Azure.Mobile.Server.Authentication]. Quindi nella classe di avvio OWIN dell'applicazione aggiungere quanto segue, dopo che `MobileAppConfiguration` è stato applicato a `HttpConfiguration`:

		app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
		{
			SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
			ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
			ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
			TokenHandler = config.GetMobileAppTokenHandler()
		});

Nell'esempio seguente è consigliabile configurare le impostazioni dell'applicazione _authAudience_ e _authIssuer_ nel file Web.config in modo che ognuna sia l'URL della radice dell'applicazione, usando lo schema HTTPS. Allo stesso modo, è consigliabile impostare _authSigningKey_ come valore della chiave per la firma dell'applicazione. Si tratta di un valore sensibile che non deve mai essere condiviso o incluso in un client. Per ottenerlo, andare all'app nel [portale di Azure] e fare clic su **Strumenti**. Selezionare quindi **Kudu** e fare clic su **Vai**. Verrà visualizzato l'endpoint di gestione di Kudu per il sito. Fare clic su **Ambiente** e trovare il valore in _WEBSITE\_AUTH\_SIGNING\_KEY_. Questo è il valore da usare per _authSigningKey_ nella configurazione dell'app locale.

Il server eseguito in locale ora è in grado di convalidare i token che il client ottiene dall'endpoint basato sul cloud.


[portale di Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=AcomDC_0309_2016-->