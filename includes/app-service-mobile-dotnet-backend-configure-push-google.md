1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server, quindi scegliere **Gestisci pacchetti NuGet**, cercare `Microsoft.Azure.NotificationHubs` e infine fare clic su **Installa**. Verrà installata la libreria client dell'Hub di notifica.

2. Nel progetto Visual Studio del back-end aprire **Controller** > **TodoItemController.cs**. Aggiungere le istruzioni `using` seguenti all'inizio del file:


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

<!---HONumber=AcomDC_1203_2015-->