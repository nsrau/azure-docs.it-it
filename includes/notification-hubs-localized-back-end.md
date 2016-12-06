



Quando si inviano notifiche modello, è necessario solo fornire un set di proprietà. In questo caso verrà inviato il set di proprietà contenente la versione localizzata delle notizie correnti, ad esempio:

    {
        "News_English": "World News in English!",
        "News_French": "World News in French!",
        "News_Mandarin": "World News in Mandarin!"
    }


In questa sezione viene illustrato come inviare notifiche tramite un’app console

Il codice incluso trasmette le notifiche sia Windows Store che a dispositivi iOS, poiché il back-end è in grado di trasmettere a qualsiasi dispositivo supportato.

### <a name="to-send-notifications-using-a-c-console-app"></a>Per inviare notifiche tramite un'app console C#
Modificare il metodo `SendTemplateNotificationAsync` nell'app console creata in precedenza con il codice seguente. Si noti come in questo caso non sia necessario inviare più notifiche per impostazioni locali e piattaforme diverse.

        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Sending the notification as a template notification. All template registrations that contain 
            // "messageParam" or "News_<local selected>" and the proper tags will receive the notifications. 
            // This includes APNS, GCM, WNS, and MPNS template registrations.
            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", "Technology", "Science", "Sports"};
            var locales = new string[] { "English", "French", "Mandarin" };

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";

                // Sending localized News for each tag too...
                foreach( var locale in locales)
                {
                    string key = "News_" + locale;

                    // Your real localized news content would go here.
                    templateParams[key] = "Breaking " + category + " News in " + locale + "!";
                }

                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
        }


Si noti che questa semplice chiamata distribuirà la notizia localizzata a **tutti** i dispositivi, indipendentemente dalla piattaforma, in quanto l'Hub di notifica crea il payload nativo corretto e lo distribuisce a tutti i dispositivi che hanno sottoscritto un tag specifico.

### <a name="sending-the-notification-with-mobile-services"></a>Invio della notifica con Servizi mobili
Nell'utilità di pianificazione di Servizi mobili, è possibile utilizzare lo script seguente:

    var azure = require('azure');
    var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
    var notification = {
            "News_English": "World News in English!",
            "News_French": "World News in French!",
            "News_Mandarin", "World News in Mandarin!"
    }
    notificationHubService.send('World', notification, function(error) {
        if (!error) {
            console.warn("Notification successful");
        }
    });




<!--HONumber=Nov16_HO3-->


