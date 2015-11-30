<properties
	pageTitle="Come usare l'SDK del server back-end .NET per App per dispositivi mobili | Servizio app di Azure"
	description="Informazioni su come usare l'SDK del server back-end .NET per App per dispositivi mobili del servizio app di Azure."
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
	ms.date="09/18/2015"
	ms.author="glenga"/>

# Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure

Questo argomento mostra come usare l'SDK del server back-end .NET in scenari chiave di App per dispositivi mobili del servizio app di Azure. Azure Mobile Apps SDK aiuta a lavorare con i client mobili dall'applicazione ASP.NET.

>[AZURE.TIP]L’[SDK del server .NET per app per dispositivi mobili di Azure](https://github.com/Azure/azure-mobile-apps-net-server) è open source su GitHub. Il repository contiene l’intero gruppo di test dell’unità SDK del server, nonché alcuni progetti di esempio.

## Procedura: Scaricare e inizializzare l'SDK

L'SDK è disponibile in [NuGet.org]. Il pacchetto include le funzionalità di base necessarie per iniziare a usare l'SDK. Per inizializzare l'SDK, è necessario eseguire alcune operazioni nell'oggetto **HttpConfiguration**.

###Installare l'SDK

Per installare l'SDK, fare clic con il pulsante destro del mouse sul progetto server in Visual Studio, scegliere **Gestisci pacchetti NuGet**, cercare il pacchetto [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) e quindi fare clic su **Installa**.

###<a name="server-project-setup"></a> Inizializzare il progetto server

Un progetto server back-end .NET viene inizializzato in modo simile ad altri progetti ASP.NET, includendo una classe di avvio OWIN. Per aggiungere questa classe in Visual Studio, fare clic con il pulsante destro del mouse sul progetto server e scegliere **Aggiungi** -> **Nuovo elemento** e quindi **Web** -> **Generale** -> **Classe di avvio OWIN**.

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

Per abilitare le singole funzionalità, è necessario chiamare i metodi di estensione nell'oggetto **MobileAppConfiguration** prima di chiamare **ApplyTo**. Ad esempio, il codice seguente aggiunge le route predefinite a tutti i controller API durante l'inizializzazione:

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

Molti metodi di estensione delle funzionalità sono disponibili tramite pacchetti NuGet aggiuntivi che è possibile includere, descritti nella sezione seguente. L'avvio rapido del server nel portale di Azure chiama **UseDefaultConfiguration()**. Questo equivale alla configurazione seguente:
    
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

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) Include le classi per usare i dati e configura la pipeline dei dati. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) Consente a Entity Framework di accedere ai dati nel database SQL. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] Consente l'autenticazione e configura il middleware OWIN usato per convalidare i token. Viene aggiunta alla configurazione chiamando i metodi di estensione **AddAppServiceAuthentication** e **IAppBuilder**.**UseMobileAppAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] Consente le notifiche push e definisce un endpoint di registrazione push. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) Crea un controller che fornisce i dati ai Web browser legacy dall'app per dispositivi mobili. Viene aggiunta alla configurazione chiamando il metodo di estensione **MapLegacyCrossDomainController**.

## Procedura: Definire un controller API personalizzato

Il controller API personalizzato fornisce le funzionalità di base per il back-end dell'app per dispositivi mobili esponendo un endpoint. Controller API personalizzato

1. In Visual Studio fare clic con il pulsante destro del mouse sulla cartella Controller e quindi scegliere **Aggiungi** > **Controller**, selezionare **Controller API Web 2 - Vuoto** e fare clic su **Aggiungi**.

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
    
	Si noti che non è necessario chiamare **MapApiControllers** se invece si chiama **UseDefaultConfiguration**, che inizializza tutte le funzionalità.

I controller a cui non è applicato **MobileAppControllerAttribute** continuano a essere accessibili da parte dei client, ma non verranno usati correttamente dai client con l'SDK client di app per dispositivi mobili.

## Procedura: Definire un controller tabelle

Un controller tabelle fornisce l'accesso ai dati delle entità in un archivio dati basato su tabelle, ad esempio il database SQL o l'Archivio tabelle di Azure. I controller della tabella ereditano dalla classe generica **TableController**, dove il tipo generico è un'entità nel modello che rappresenta lo schema della tabella, come indicato di seguito:

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

I controller della tabella vengono inizializzati usando il metodo di estensione **AddTables**. L'esempio seguente inizializza un controller tabelle che usa Entity Framework per accedere ai dati:

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Per un esempio di un controller di tabella che usa Entity Framework per accedere ai dati da un database SQL di Azure, vedere la classe **TodoItemController** nel progetto server di avvio rapido che è possibile scaricare dal portale di Azure.

## Procedura: Aggiungere l'autenticazione a un progetto server

È possibile aggiungere l'autenticazione al progetto server estendendo l'oggetto **MobileAppConfiguration** e configurando il middleware OWIN. Quando si installa il pacchetto [Microsoft.Azure.Mobile.Server.Quickstart] e si chiama il metodo di estensione **UseDefaultConfiguration**, è possibile andare al passaggio 3.

1. In Visual Studio installare il pacchetto [Microsoft.Azure.Mobile.Server.Authentication]. 

2. Nel file di progetto Startup.cs aggiungere la riga di codice seguente all'inizio del metodo **Configuration**:

		app.UseMobileAppAuthentication(config);

	In questo modo viene aggiunto il componente middleware OWIN, che consente all'app per dispositivi mobili di Azure di convalidare i token rilasciati dal gateway del servizio app associato.

3. Aggiungere l'attributo `[Authorize]` a qualsiasi controller o metodo che richiede l'autenticazione. Gli utenti devono ora essere autenticati per accedere a tale endpoint o a tali API specifiche.

Per informazioni su come autenticare i client nel back-end di app per dispositivi mobili, vedere l'articolo relativo all'[aggiunta dell'autenticazione a un'app](app-service-mobile-ios-get-started-users.md).

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

## Procedura: Aggiungere tag all'installazione di un dispositivo per eseguire il push dei tag

Dopo aver eseguito il passaggio precedente **Procedura: Definire un controller API personalizzato**, è possibile configurare un'API personalizzata nel back-end che userà Hub di notifica per aggiungere tag all'installazione di un dispositivo specifico. Assicurarsi di passare l'ID installazione memorizzato nella risorsa di archiviazione locale client assieme ai tag che si desidera aggiungere (l'aggiunta è facoltativa perché è anche possibile specificare i tag direttamente nel back-end). Il frammento di codice seguente deve essere aggiunto al controller che userà gli hub di notifica per aggiungere un tag all'ID di installazione di un dispositivo.

Usando il [pacchetto NuGet degli hub di notifica di Azure](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)([riferimento](https://msdn.microsoft.com/library/azure/mt414893.aspx)):

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
	

Per eseguire il push di questi tag, usare le [API degli hub di notifica](https://msdn.microsoft.com/library/azure/dn495101.aspx).

È anche possibile organizzare l'API personalizzata in modo da registrare le installazioni dei dispositivi con gli hub di notifica direttamente nel back-end.

## Procedura: Pubblicare il progetto server

Eseguire la procedura seguente per pubblicare il progetto server in Azure:

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]


[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=Nov15_HO4-->