Usare la procedura corrispondente al tipo di progetto di back-end in corso: [back-end .NET](#dotnet) o [back-end Node. js](#nodejs).

### <a name="dotnet"></a>Progetto di back-end .NET

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server, quindi scegliere **Gestisci pacchetti NuGet**, cercare `Microsoft.Azure.NotificationHubs` e infine fare clic su **Installa**. Verrà installata la libreria client dell'Hub di notifica.

2. Nella cartella Controller aprire TodoItemController.cs e aggiungere le istruzioni `using` seguenti:

        using Microsoft.Azure.Mobile.Server.Config;
        using Microsoft.Azure.NotificationHubs;

3. Sostituire il metodo `PostTodoItem` con il codice seguente:

      
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
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

            // Android payload
            var androidNotificationPayload = "{ "data" : {"message":"" + item.Text + ""}}";

            try
            {
                // Send the push notification and log the results.
                var result = await hub.SendGcmNativeNotificationAsync(androidNotificationPayload);

                // Write the success result to the logs.
                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                // Write the failure result to the logs.
                config.Services.GetTraceWriter()
                    .Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

### <a name="nodejs"></a>Progetto di back-end Node.js

1. [Scaricare il progetto di avvio rapido](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) (se non ancora scaricato) oppure usare l'[editor online del portale di Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
 
1. Sostituire il codice esistente nel file todoitem.js file con il codice seguente:

		var azureMobileApps = require('azure-mobile-apps'),
		promises = require('azure-mobile-apps/src/utilities/promises'),
		logger = require('azure-mobile-apps/src/logger');
		
		var table = azureMobileApps.table();
		
		table.insert(function (context) {
		// For more information about the Notification Hubs JavaScript SDK, 
		// see http://aka.ms/nodejshubs
		logger.info('Running TodoItem.insert');
		
		// Define the GCM payload.
		var payload = {
		    "data": {
		        "message": context.item.text
		    }
		};   
		
		// Execute the insert.  The insert returns the results as a Promise,
		// Do the push as a post-execute action within the promise flow.
		return context.execute()
		    .then(function (results) {
		        // Only do the push if configured
		        if (context.push) {
					// Send a GCM native notification.
		            context.push.gcm.send(null, payload, function (error) {
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

	Ogni volta che viene inserito un nuovo elemento Todo, viene inviata una notifica GCM contenente l'elemento item.text.

2. Quando si modifica il file nel computer locale, ripubblicare il progetto server.

<!---HONumber=AcomDC_1210_2015-->