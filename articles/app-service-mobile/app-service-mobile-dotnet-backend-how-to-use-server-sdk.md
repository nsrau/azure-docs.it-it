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
	ms.date="12/03/2015"
	ms.author="glenga"/>

# Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

Questo argomento mostra come usare l'SDK del server back-end .NET in scenari chiave di App per dispositivi mobili del servizio app di Azure. Azure Mobile Apps SDK aiuta a lavorare con i client mobili dall'applicazione ASP.NET.

>[AZURE.TIP]L'[SDK del server .NET per App per dispositivi mobili di Azure](https://github.com/Azure/azure-mobile-apps-net-server) è open source su GitHub. Il repository contiene l’intero gruppo di test dell’unità SDK del server, nonché alcuni progetti di esempio.

## Documentazione di riferimento

La documentazione di riferimento per l'SDK del server è disponibile qui: [Riferimento .NET di App per dispositivi mobili di Azure](https://msdn.microsoft.com/library/azure/dn961176.aspx).

## <a name="create-app"></a>Procedura: Creare un back-end .NET per l'applicazione per dispositivi mobili

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

5. In _Modelli ASP.NET 4.5.2_ selezionare **App per dispositivi mobili di Azure**. Selezionare **Host nel cloud** per creare una nuova app per dispositivi mobili nel cloud in cui è possibile pubblicare questo progetto.

6. Fare clic su **OK**. L'applicazione verrà creata e visualizzata in Esplora soluzioni.

## <a name="install-sdk"></a>Procedura: Scaricare e inizializzare l'SDK

L'SDK è disponibile in [NuGet.org]. Il pacchetto include le funzionalità di base necessarie per iniziare a usare l'SDK. Per inizializzare l'SDK, è necessario eseguire alcune operazioni sull'oggetto **HttpConfiguration**.

###Installare l'SDK

Per installare l'SDK, fare clic con il pulsante destro del mouse sul progetto server in Visual Studio, scegliere **Gestisci pacchetti NuGet**, cercare il pacchetto [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) e quindi fare clic su **Installa**.

###<a name="server-project-setup"></a> Inizializzare il progetto server

Un progetto server back-end .NET viene inizializzato in modo simile ad altri progetti ASP.NET, includendo una classe di avvio OWIN. Assicurarsi che si sia fatto riferimento al pacchetto NuGet `Microsoft.Owin.Host.SystemWeb`. Per aggiungere questa classe in Visual Studio, fare clic con il pulsante destro del mouse sul progetto server e scegliere **Aggiungi** -> **Nuovo elemento**, quindi **Web** -> **Generale** -> **Classe di avvio OWIN**.

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

- [Microsoft.Azure.Mobile.Server.Quickstart] supporta la configurazione di base di app per dispositivi mobili. Viene aggiunta alla configurazione chiamando il metodo di estensione **UseDefaultConfiguration** durante l'inizializzazione. Questa estensione include le estensioni seguenti: pacchetti Notifications, Authentication, Entity, Tables, Crossdomain e Home. È equivalente al progetto server di avvio rapido che è possibile scaricare dal portale di Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implementa la pagina predefinita *questa app per dispositivi mobili è in esecuzione * per la radice del sito Web. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) include le classi per usare i dati e configura la pipeline dei dati. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) consente a Entity Framework di accedere ai dati nel database SQL. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] consente l'autenticazione e configura il middleware OWIN usato per convalidare i token. Viene aggiunta alla configurazione chiamando i metodi di estensione **AddAppServiceAuthentication** e **IAppBuilder**.**UseMobileAppAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] consente le notifiche push e definisce un endpoint di registrazione push. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) crea un controller che fornisce i dati ai Web browser legacy dall'app per dispositivi mobili. Viene aggiunta alla configurazione chiamando il metodo di estensione **MapLegacyCrossDomainController**.

- [Microsoft.Azure.Mobile.Server.Login] fornisce il supporto di anteprima per l'autenticazione personalizzata con il metodo MobileAppLoginHandler.CreateToken(). Si tratta di un metodo statico che non è necessario abilitare nella configurazione.

## Procedura: Pubblicare il progetto server

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

È anche possibile usare uno degli altri metodi illustrati nella [documentazione sulla distribuzione del servizio app di Azure](../app-service-web/web-site-deploy.md).

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
 
Per un esempio di un controller di tabella che usa Entity Framework per accedere ai dati da un database SQL di Azure, vedere la classe **TodoItemController** nel progetto server di avvio rapido che è possibile scaricare dal portale di Azure.

## Procedura: Definire un controller API personalizzato

Il controller API personalizzato fornisce le funzionalità di base per il back-end dell'app per dispositivi mobili esponendo un endpoint. È possibile registrare un controller di API specifico per dispositivi mobili usando l'attributo `MobileAppControllerAttribute`. Questo attributo registra la route e configura anche il serializzatore JSON delle app per dispositivi mobili.

1. In Visual Studio fare clic con il pulsante destro del mouse sulla cartella Controller e quindi scegliere **Aggiungi** > **Controller**, selezionare **Controller API Web 2&mdash;Vuoto** e fare clic su **Aggiungi**.

2. Specificare un nome in **Nome controller**, ad esempio `CustomController`, e fare clic su **Aggiungi**. Verrà creata una nuova classe **CustomController** che eredita da **ApiController**.

3. Nella nuovo file della classe controller aggiungere l'istruzione using seguente:

		using Microsoft.Azure.Mobile.Server.Config;

4. Applicare **MobileAppControllerAttribute** alla definizione della classe controller API, come nell'esempio seguente:

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

I controller a cui non è applicato **MobileAppControllerAttribute** continuano a essere accessibili da parte dei client, ma potrebbero non essere usati correttamente dai client con l'SDK client di app per dispositivi mobili.


## Procedura: Aggiungere l'autenticazione a un progetto server

È possibile aggiungere l'autenticazione al progetto server estendendo l'oggetto **MobileAppConfiguration** e configurando il middleware OWIN. Quando si installa il pacchetto [Microsoft.Azure.Mobile.Server.Quickstart] e si chiama il metodo di estensione **UseDefaultConfiguration**, è possibile andare al passaggio 3.

1. In Visual Studio installare il pacchetto [Microsoft.Azure.Mobile.Server.Authentication]. 

2. Nel file di progetto Startup.cs aggiungere la riga di codice seguente all'inizio del metodo **Configuration**:

		app.UseMobileAppAuthentication(config);

	In questo modo viene aggiunto il componente middleware OWIN, che consente all'app per dispositivi mobili di Azure di convalidare i token rilasciati dal gateway del servizio app associato.

3. Aggiungere l'attributo `[Authorize]` a qualsiasi controller o metodo che richiede l'autenticazione. Gli utenti devono ora essere autenticati per accedere a tale endpoint o ad API specifiche.

Per informazioni su come autenticare i client nel back-end di app per dispositivi mobili, vedere l'articolo [Aggiungere l'autenticazione all'app](app-service-mobile-ios-get-started-users.md).

## <a name="custom-auth"></a>Procedura: Usare l'autenticazione personalizzata per la propria applicazione

È possibile scegliere di fornire il proprio sistema di accesso se non si vuole usare uno dei provider di autenticazione/autorizzazione del servizio app. A questo scopo, installare il pacchetto [Microsoft.Azure.Mobile.Server.Login].

Sarà necessario fornire la propria logica per determinare se un utente deve essere registrato. È possibile, ad esempio, confrontare le password con salting e hashing in un database. Nell'esempio seguente il metodo `isValidAssertion()` è responsabile di questi controlli e viene definito altrove.

L'autenticazione personalizzata viene esposta creando un nuovo ApiController ed esponendo le azioni di registrazione e accesso come quella seguente. Il client può tentare l'accesso raccogliendo le informazioni pertinenti dall'utente e inviando un POST HTTPS all'API con le informazioni utente nel corpo. Dopo la convalida di queste informazioni, è possibile rilasciare un token con il metodo `MobileAppLoginHandler.CreateToken()`.

Ecco un esempio di azione di accesso:

		public HttpResponseMessage Post([FromBody] JObject assertion)
		{
			if (isValidAssertion(assertion)) // user-defined function, checks against a database
			{
				JwtSecurityToken token = MobileAppLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
					mySigningKey,
					myAppURL,
					myAppURL,
					TimeSpan.FromHours(24) );
				return this.Request.CreateResponse(HttpStatusCode.OK, new LoginResult()
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

Il metodo `MobileAppLoginHandler.CreateToken()` include un parametro _audience_ e un parametro _issuer_. Entrambi vengono in genere impostati sull'URL della radice dell'applicazione, usando lo schema HTTPS Allo stesso modo, è consigliabile impostare _secretKey_ come valore della chiave per la firma dell'applicazione. Si tratta di un valore sensibile che non deve mai essere condiviso o incluso in un client. È possibile ottenere questo valore mentre è ospitato nel servizio app facendo riferimento alla variabile di ambiente _WEBSITE\_AUTH\_SIGNING\_KEY_. Se necessario in un contesto di debug locale, seguire le istruzioni nella sezione [Debug locale con autenticazione](#local-debug) per recuperare la chiave e archiviarla come impostazione dell'applicazione.

È anche necessario fornire una durata per il token rilasciato, oltre a eventuali attestazioni che si vuole includere. È necessario fornire un'attestazione dell'oggetto, come mostrato nel codice di esempio.

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

A questo punto, è possibile usare il client di Hub di notifica per inviare notifiche push ai dispositivi registrati. Per altre informazioni, vedere l'articolo [Aggiungere notifiche push all'app iOS](app-service-mobile-ios-get-started-push.md). Per altre informazioni su tutte le operazioni disponibili con Hub di notifica, vedere [Panoramica dell'Hub di notifica di Azure](../notification-hubs/notification-hubs-overview.md).

##<a name="tags"></a>Procedura: Aggiungere tag all'installazione di un dispositivo per abilitare il push dei tag

Dopo avere eseguito il passaggio precedente **Procedura: Definire un controller API personalizzato**, è possibile configurare un'API personalizzata nel back-end che userà Hub di notifica per aggiungere tag all'installazione di un dispositivo specifico. Assicurarsi di passare l'ID installazione memorizzato nella risorsa di archiviazione locale client assieme ai tag che si desidera aggiungere (l'aggiunta è facoltativa perché è anche possibile specificare i tag direttamente nel back-end). Il frammento di codice seguente deve essere aggiunto al controller che userà gli hub di notifica per aggiungere un tag all'ID di installazione di un dispositivo.

Usare il [pacchetto NuGet di Hub di notifica di Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) ([riferimento](https://msdn.microsoft.com/library/azure/mt414893.aspx)):

		var hub = NotificationHubClient.CreateClientFromConnectionString("my-connection-string", "my-hub");

		hub.PatchInstallation("my-installation-id", new[]
		{
		    new PartialUpdateOperation
		    {
		        Operation = UpdateOperationType.Add,
		        Path = "/tags",
		        Value = "{my-tag}"
		    }
		});

Per eseguire il push di questi tag, usare le [API di Hub di notifica](https://msdn.microsoft.com/library/azure/dn495101.aspx).

È anche possibile organizzare l'API personalizzata in modo da registrare le installazioni dei dispositivi con gli hub di notifica direttamente nel back-end.

## Procedura: Eseguire il debug e risolvere i problemi di .NET Server SDK

Il servizio app di Azure offre diverse tecniche di debug e risoluzione dei problemi per le applicazioni ASP.NET:

- [Monitoraggio di un servizio app di Azure](../app-service-web/web-sites-monitor.md)
- [Abilitazione della registrazione diagnostica nel servizio app di Azure](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Risoluzione dei problemi di un Servizio app di Azure in Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### Registrazione

È possibile scrivere nei log di diagnostica del servizio app usando la scrittura di tracce ASP.NET standard:

		ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
		traceWriter.Info("Hello, World");  

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

<!---HONumber=AcomDC_1210_2015-->