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
	ms.date="08/12/2015"
	ms.author="glenga"/>

# Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure

Questo argomento mostra come usare l'SDK del server back-end .NET in scenari chiave di App per dispositivi mobili del servizio app di Azure. Azure Mobile Apps SDK aiuta a lavorare con i client mobili dall'applicazione ASP.NET.

## Procedura: Scaricare e inizializzare l'SDK

L'SDK è disponibile in [NuGet.org]. Il pacchetto include le funzionalità di base necessarie per iniziare a usare l'SDK. Per inizializzare l'SDK, è necessario eseguire alcune operazioni nell'oggetto **HttpConfiguration**.

###Installare l'SDK

Per installare l'SDK, fare clic con il pulsante destro del mouse sul progetto server in Visual Studio, scegliere **Gestisci pacchetti NuGet**, cercare il pacchetto [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) e quindi fare clic su **Installa**.

###Inizializzare il progetto server

Un progetto server back-end .NET viene inizializzato nel metodo **Register** della classe **WebApiConfig**, disponibile in genere nella cartella App\_Start. Per inizializzare il progetto server, viene usato un oggetto **HttpConfiguration**, che rappresenta le opzioni di configurazione per il servizio. L'esempio seguente inizializza il progetto server, senza funzionalità aggiuntive:

    new MobileAppConfiguration()
        .ApplyTo(config);

Per abilitare le singole funzionalità, è necessario chiamare i metodi di estensione nell'oggetto **MobileAppConfiguration** prima di chiamare **ApplyTo**. Ad esempio, il codice seguente aggiunge le route predefinite a tutti i controller API durante l'inizializzazione:

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

Molti metodi di estensione delle funzionalità sono disponibili tramite pacchetti NuGet aggiuntivi che è possibile includere, descritti nella sezione seguente.

### Estensioni SDK

I pacchetti di estensione basati su NuGet seguenti forniscono diverse funzionalità per dispositivi mobili che l'applicazione può usare. È possibile abilitare le estensioni durante l'inizializzazione usando l'oggetto **MobileAppConfiguration**.

- [Microsoft.Azure.Mobile.Server.Quickstart] Supporta la configurazione di base di App per dispositivi mobili. Viene aggiunta alla configurazione chiamando il metodo di estensione **UseDefaultConfiguration** durante l'inizializzazione. Questa estensione include le estensioni seguenti: pacchetti Notifications, Authentication, Entity, Tables, Crossdomain e Home. È equivalente al progetto server di avvio rapido che è possibile scaricare dal portale di Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) Aggiunge una semplice home page alla directory principale del sito Web. Viene aggiunta alla configurazione chiamando il metodo di estensione **AddMobileAppHomeController**.

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

4. Passare alla cartella App\_Startup, aprire il file di progetto WebApiConfig.cs e aggiungere una chiamata al metodo di estensione **MapApiControllers**, come nell'esempio seguente:

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);

	Si noti che non è necessario chiamare **MapApiControllers** quando si chiama **UseDefaultConfiguration**, che inizializza tutte le funzionalità.

Qualsiasi controller a cui non è applicato **MobileAppControllerAttribute** è ancora accessibile da parte dei client, ma non verrà utilizzato correttamente dai client con l'SDK client di un'app per dispositivi mobili.

## Procedura: Definire un controller tabelle

Un controller tabelle fornisce l'accesso ai dati delle entità in un archivio dati basato su tabelle, ad esempio il database SQL o l'Archivio tabelle di Azure. I controller tabelle ereditano dalla classe generica **TableController**, dove il tipo generico è un'entità nel modello che rappresenta lo schema della tabella, come indicato di seguito:

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

I controller tabelle vengono inizializzati usando il metodo di estensione **AddTables**. L'esempio seguente inizializza un controller tabelle che usa Entity Framework per accedere ai dati:

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Per un esempio di un controller tabelle che usa Entity Framework per accedere ai dati da un database SQL di Azure, vedere la classe **TodoItemController** nel progetto server di avvio rapido che è possibile scaricare dal portale di Azure.

## Procedura: Aggiungere l'autenticazione a un progetto server

È possibile aggiungere l'autenticazione al progetto server estendendo l'oggetto **MobileAppConfiguration** e configurando il middleware OWIN. Quando si installa il pacchetto [Microsoft.Azure.Mobile.Server.Quickstart] e si chiama il metodo di estensione **UseDefaultConfiguration**, è possibile andare al passaggio 4.

1. In Visual Studio installare il pacchetto [Microsoft.Azure.Mobile.Server.Authentication]. 

2. Passare alla cartella App\_Startup, aprire il file di progetto WebApiConfig.cs e aggiungere una chiamata al metodo di estensione **AddAppServiceAuthentication** durante l'inizializzazione, come nell'esempio seguente:

		new MobileAppConfiguration()
			// other features...
			.AddAppServiceAuthentication()
			.ApplyTo(config);

3. Nel file di progetto Startup.cs aggiungere la riga di codice seguente all'inizio del metodo **Configuration**:

		app.UseMobileAppAuthentication(config);

	In questo modo viene aggiunto il componente middleware OWIN, che consente all'app per dispositivi mobili di Azure di convalidare i token rilasciati dal gateway del servizio app associato.

4. Aggiungere l'attributo `[Authorize]` a qualsiasi controller o metodo che richiede l'autenticazione. Gli utenti devono ora essere autenticati per accedere a tale endpoint o a tali API specifiche.

Per informazioni su come autenticare i client nel back-end di App per dispositivi mobili, vedere [Aggiungere l'autenticazione all'app](app-service-mobile-dotnet-backend-ios-get-started-users-preview.md).

## Procedura: Aggiungere notifiche push a un progetto server

È possibile aggiungere notifiche push al progetto server estendendo l'oggetto **MobileAppConfiguration** e creando un client di Hub di notifica. Quando si installa il pacchetto [Microsoft.Azure.Mobile.Server.Quickstart] e si chiama il metodo di estensione **UseDefaultConfiguration**, è possibile andare al passaggio 3.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server e quindi scegliere **Gestisci pacchetti NuGet**, cercare Microsoft.Azure.Mobile.Server.Notifications e infine fare clic su **Installa**. In questo modo viene installato il pacchetto [Microsoft.Azure.Mobile.Server.Notifications].
 
3. Ripetere questo passaggio per installare il pacchetto `Microsoft.Azure.NotificationHubs`, che include la libreria client di Hub di notifica.

2. Passare alla cartella App\_Startup, aprire il file di progetto WebApiConfig.cs e aggiungere una chiamata al metodo di estensione **AddPushNotifications** durante l'inizializzazione, come nell'esempio seguente:

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
        ServiceSettingsDictionary settings = 
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

A questo punto, è possibile usare il client di Hub di notifica per inviare notifiche push ai dispositivi registrati. Per altre informazioni, vedere [Aggiungere notifiche push all'app](app-service-mobile-dotnet-backend-ios-get-started-push-preview.md). Per altre informazioni su tutto ciò che è possibile fare con Hub di notifica, vedere [Panoramica di Hub di notifica](../notification-hubs/notification-hubs-overview.md).

## Procedura: Pubblicare il progetto server

Eseguire la procedura seguente per pubblicare il progetto server in Azure:

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]


[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=August15_HO8-->