Usare la procedura corrispondente al tipo di progetto di back-end in corso: [back-end .NET](#dotnet) o [back-end Node. js](#nodejs).

### <a name="dotnet"></a>Progetto di back-end .NET
1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server, quindi scegliere **Gestisci pacchetti NuGet**, cercare `Microsoft.Azure.NotificationHubs` e infine fare clic su **Installa**. Consente di installare la libreria di hub di notifica per l'invio di notifiche dal back-end.

3. Nel progetto server aprire **Controller** > **TodoItemController.cs** e quindi aggiungere le istruzioni using seguenti:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. Nel metodo **PostTodoItem** aggiungere il codice seguente dopo la chiamata a **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
			this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Questo codice comunica a hub di notifica di inviare una notifica modello a tutte le registrazioni di modello che contengono "messageParam". La stringa verrà inserita al posto di messageParam in ogni PNS che ha una registrazione che utilizza "messageParam". Ciò consente di inviare la notifica a APNS, GCM, WNS o a qualsiasi altro PNS.

	Per ulteriori informazioni sui modelli con Hub di notifica, vedere [Modelli](notification-hubs-templates.md).

4. Pubblicare di nuovo il progetto server.

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
	    
	    // Define the template payload.
	    var payload = '{"messageParam":' + context.item.text + '}'; 
	    
	    // Execute the insert.  The insert returns the results as a Promise,
	    // Do the push as a post-execute action within the promise flow.
	    return context.execute()
	        .then(function (results) {
	            // Only do the push if configured
	            if (context.push) {
					// Send a template notification.
	                context.push.send(null, payload, function (error) {
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

	Ogni volta che viene inserito un nuovo elemento Todo, viene inviata una notifica modello contenente l'elemento item.text.

2. Quando si modifica il file nel computer locale, ripubblicare il progetto server.
