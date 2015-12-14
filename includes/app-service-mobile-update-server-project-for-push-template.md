
1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server, quindi scegliere **Gestisci pacchetti NuGet**, cercare `Microsoft.Azure.NotificationHubs` e infine fare clic su **Installa**. Consente di installare la libreria di hub di notifica per l'invio di notifiche dal back-end.

3. Nel progetto server aprire **Controller** > **TodoItemController.cs**, quindi aggiungere le istruzioni using seguenti:

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

	Per ulteriori informazioni sui modelli con hub di notifica, vedere [Modelli](notification-hubs-templates.md).

<!---HONumber=AcomDC_1203_2015-->