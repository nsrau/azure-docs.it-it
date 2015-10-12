<properties 
	pageTitle="Inviare notifiche push a un utente specifico con client Xamarin iOS" 
	description="Informazioni su come inviare notifiche push a tutti i dispositivi di un utente" 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="ysxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/25/2015"
	ms.author="yuaxu"/>

# Inviare notifiche multipiattaforma a un utente specifico

[AZURE.INCLUDE [app-service-mobile-selector-push-users-preview](../../includes/app-service-mobile-selector-push-users-preview.md)]& nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

Questo argomento descrive come inviare notifiche a tutti i dispositivi registrati di un utente specifico dal proprio back-end mobile. È stato introdotto il concetto di [modelli], che offre alle applicazioni client la libertà di specificare i formati di payload e segnaposto di variabili al momento della registrazione. L'operazione di invio raggiunge tutte le piattaforme grazie a questi segnaposto, abilitando le notifiche multipiattaforma.

> [AZURE.NOTE]Affinché il push funzioni con i client multipiattaforma, sarà necessario completare questa esercitazione per ogni piattaforma che si vuole abilitare. Sarà necessario effettuare l'[aggiornamento del back-end mobile](#backend) solo una volta per i client che condividono lo stesso back-end mobile.
 
##Prerequisiti 

Prima di iniziare questa esercitazione, è necessario aver già completato le esercitazioni seguenti sul servizio app per ogni piattaforma client con cui si vuole lavorare:

+ [Introduzione all'autenticazione]<br/>Aggiunge un requisito di accesso all'app di esempio TodoList.

+ [Introduzione alle notifiche push]<br/>Configura l'app di esempio TodoList per le notifiche push.

##<a name="client"></a>Aggiornare il client per registrare i modelli per la gestione di notifiche push multipiattaforma

1. Spostare i frammenti di codice di registrazione del servizio APN da **FinishedLaunching** a **AppDelegate.cs** nella definizione di attività **RefreshAsync** in **QSTodoListViewController.cs**. Le registrazioni devono avvenire dopo il completamento dell'autenticazione.

        ...
        if (todoService.User == null) {
            await QSTodoService.DefaultService.Authenticate (this);
            if (todoService.User == null) {
                Console.WriteLine ("couldn't login!!");
                return;
            }

            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes (
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound, 
                new NSSet ());

            UIApplication.SharedApplication.RegisterUserNotificationSettings (settings); 
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        ...

2. In **AppDelegate.cs** sostituire la chiamata **RegisterAsync** in **RegisteredForRemoteNotifications** con il codice seguente per il funzionamento con i modelli:

        // delete await push.RegisterAsync (deviceToken);
        
        var notificationTemplate = "{"aps": {"alert":"$(message)"}}";

        JObject templateBody = new JObject();
        templateBody["body"] = notificationTemplate;

        JObject templates = new JObject();
        templates["testApnsTemplate"] = templateBody;

        // register with templates
        await push.RegisterAsync (deviceToken, templates);

##<a name="backend"></a>Aggiornare il back-end del servizio per inviare notifiche a un utente specifico

1. In Visual Studio aggiornare la definizione del metodo `PostTodoItem` con il codice seguente:  

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // get notification hubs credentials associated with this mobile app
            string notificationHubName = this.Services.Settings.NotificationHubName;
            string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

            // connect to notification hub
            NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

            // get the current user id and create given user tag
            ServiceUser authenticatedUser = this.User as ServiceUser;
            string userTag = "_UserId:" + authenticatedUser.Id;

            var notification = new Dictionary<string, string>{{"message", item.Text}};

            try
            {
                await Hub.Push.SendTemplateNotificationAsync(notification, userTag);
            }
            catch (System.Exception ex)
            {
                throw;
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

##<a name="test"></a>Test dell'app

Pubblicare di nuovo il progetto di back-end mobile ed eseguire una qualsiasi delle app client impostate. Quando viene inserito l'elemento, il back-end invierà le notifiche a tutte le app client alle quali l'utente è connesso.

<!-- URLs. -->
[Introduzione all'autenticazione]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md
[Introduzione alle notifiche push]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-push-preview.md
[modelli]: https://msdn.microsoft.com/it-IT/library/dn530748.aspx

<!---HONumber=Oct15_HO1-->