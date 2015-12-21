<properties 
	pageTitle="Aggiungere notifiche push all'app universale di Windows Runtime 8.1 | App per dispositivi mobili di Azure" 
	description="Informazioni su come usare le app per dispositivi mobili del servizio app di Azure e gli hub di notifica di Azure per inviare notifiche push all'app di Windows." 
	services="app-service\mobile,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/25/2015" 
	ms.author="glenga"/>

# Aggiungere notifiche push all'app universale di Windows Runtime 8.1

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Panoramica

Questo argomento descrive come inviare notifiche push a un'app universale di Windows Runtime 8.1 tramite le app per dispositivi mobili del servizio app di Azure e gli hub di notifica di Azure. In questo scenario, quando viene aggiungo un nuovo elemento, il back-end dell'app per dispositivi mobili invia una notifica push a tutte le app di Windows registrate al servizio di notifica Windows.

Questa esercitazione è basata sulla guida introduttiva delle app per dispositivi mobili del servizio app. Prima di iniziare questa esercitazione, è necessario completare l'esercitazione relativa alla guida introduttiva [Creare un'app di Windows](../app-service-mobile-windows-store-dotnet-get-started.md). Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di notifica push al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un [account di Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) attivo.
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* Completare l'[esercitazione della guida introduttiva](../app-service-mobile-windows-store-dotnet-get-started.md).  


##<a name="create-hub"></a>Creare un hub di notifica

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Registrare l'app per le notifiche push

Per potere inviare notifiche push alle app di Windows da Azure, è prima di tutto necessario inviare l'app a Windows Store. Sarà quindi possibile configurare il progetto server per l'integrazione con il servizio di notifica Windows.

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


##Configurare il back-end per l'invio di notifiche push

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>Aggiornare il server per l'invio di notifiche push

Ora che le notifiche push sono abilitate nell'app, è necessario aggiornare il back-end dell'app per l'invio delle notifiche push. Usare la procedura corrispondente al tipo di progetto di back-end in corso: [back-end .NET](#dotnet) o [back-end Node. js](#nodejs).

### <a name="dotnet"></a>Progetto di back-end .NET

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server, scegliere **Gestisci pacchetti NuGet**, cercare Microsoft.Azure.NotificationHubs e quindi fare clic su **Installa**. Verrà installata la libreria client dell'Hub di notifica.

2. Espandere **Controller**, aprire TodoItemController.cs e aggiungere le istruzioni using seguenti:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;

3. Nel metodo **PostTodoItem** aggiungere il codice seguente dopo la chiamata a **InsertAsync**:

	    // Get the settings for the server project.
	    HttpConfiguration config = this.Configuration;
	    MobileAppSettingsDictionary settings =
	        this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
	
	    // Get the Notification Hubs credentials for the Mobile App.
	    string notificationHubName = settings.NotificationHubName;
	    string notificationHubConnection = settings
	        .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;
	
	    // Create the notification hub client.
	    NotificationHubClient hub = NotificationHubClient
	        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);
	
	    // Define a WNS payload
	    var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
	                            + item.Text + @"</text></binding></visual></toast>";
	    try
	    {
	        // Send the push notification.
	        var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);
	
	        // Write the success result to the logs.
	        config.Services.GetTraceWriter().Info(result.State.ToString());
	    }
	    catch (System.Exception ex)
	    {
	        // Write the failure result to the logs.
	        config.Services.GetTraceWriter()
	            .Error(ex.Message, null, "Push.SendAsync Error");
	    }
	
	Questo codice indica all'hub di notifica di inviare una notifica push dopo l'inserimento di un nuovo elemento.

### <a name="nodejs"></a>Progetto di back-end Node.js

1. [Scaricare il progetto di avvio rapido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) (se non ancora scaricato) oppure usare l'[editor online del portale di Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Sostituire il codice esistente nel file todoitem.js file con il codice seguente:

		var azureMobileApps = require('azure-mobile-apps'),
	    promises = require('azure-mobile-apps/src/utilities/promises'),
	    logger = require('azure-mobile-apps/src/logger');
	
		var table = azureMobileApps.table();
		
		table.insert(function (context) {
	    // For more information about the Notification Hubs JavaScript SDK,  
	    // see http://aka.ms/nodejshubs
	    logger.info('Running TodoItem.insert');
	    
	    // Define the WNS payload that contains the new item Text.
	    var payload = "<toast><visual><binding template=\ToastText01><text id="1">"
		                            + context.item.text + "</text></binding></visual></toast>";
	    
	    // Execute the insert.  The insert returns the results as a Promise,
	    // Do the push as a post-execute action within the promise flow.
	    return context.execute()
	        .then(function (results) {
	            // Only do the push if configured
	            if (context.push) {
					// Send a WNS native toast notification.
	                context.push.wns.sendToast(null, payload, function (error) {
	                    if (error) {
	                        logger.error('Error while sending push notification: ', error);
	                    } else {
	                        logger.info('Push notification sent successfully!');
	                    }
	                });
	            }
	            // Don't forget to return the results from the context.execute()
	            return results;
	        })
	        .catch(function (error) {
	            logger.error('Error while running context.execute: ', error);
	        });
		});

		module.exports = table;  

	Ogni volta che viene inserito un nuovo elemento Todo, viene inviata una notifica popup WNS contenente l'elemento item.text.

2. Quando si modifica il file nel computer locale, ripubblicare il progetto server.

## <a name="publish-the-service"></a>Pubblicare il back-end mobile in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##<a id="update-service"></a>Aggiungere notifiche push all'app

1. Aprire il file di progetto condiviso **App.xaml.cs** e aggiungere le istruzioni `using` seguenti:

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

2. Nello stesso file aggiungere la seguente definizione del metodo **InitNotificationsAsync** alla classe **App**:
    
        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Questo codice consente di recuperare il valore di ChannelURI per l'app da Servizi notifica Push Windows e quindi di registrarlo con l'app per dispositivi mobili del servizio app.
    
3. All'inizio del gestore eventi **OnLaunched** nel file **App.xaml.cs** aggiungere il modificatore **async** alla definizione del metodo e quindi aggiungere la chiamata seguente al nuovo metodo **InitNotificationsAsync**, come illustrato di seguito:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    In tal modo si garantirà che il valore di ChannelURI temporaneo venga registrato ogni volta che l'applicazione viene avviata.

4. In Esplora soluzioni fare doppio clic sul file **Package.appxmanifest** dell'app di Windows Store e in **Notifiche** impostare **Popup supportati** su **Sì**.

    Scegliere **Salva tutto** dal menu **File**.

5. Ripetere il passaggio precedente nel progetto di app di Windows Phone Store.

L'app è ora pronta per ricevere notifiche di tipo avviso popup.

##<a id="test"></a>Testare le notifiche push nell'app

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


##<a id="more"></a>Altro

* I modelli offrono flessibilità per inviare notifiche push multipiattaforma e push localizzati. [Come usare il client gestito per App per dispositivi mobili di Azure](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications) illustra come registrare modelli.
* I tag consentono di orientarsi a clienti segmentati con notifiche push. [Usare l'SDK del server back-end .NET per App per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) illustra come aggiungere tag all'installazione di un dispositivo.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=AcomDC_1210_2015--->