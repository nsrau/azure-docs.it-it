
In questa sezione si invieranno notizie aggiornate come notifiche modello con tag da un'app console .NET.

Se si usa la funzionalità App per dispositivi mobili di Servizio app di Microsoft Azure, vedere l'esercitazione [Add push notifications for Mobile Apps] (Aggiungere notifiche push per App per dispositivi mobili) e selezionare la piattaforma in uso nella parte superiore della pagina.

Se si vuole usare Java o PHP, vedere [How to use Notification Hubs from Java or PHP] (Come usare Hub di notifica da Java o PHP). È possibile inviare notifiche da qualsiasi back-end tramite l'[interfaccia REST di Hub di notifica].

Se è stata creata l'app console per l'invio di notifiche al termine dell'esercitazione [Get started with Notification Hubs] (Introduzione a Hub di notifica), ignorare i passaggi da 1 a 3.

1. In Visual Studio creare una nuova applicazione console in Visual C#:
   
      ![Collegamento all'app console][13]

2. Dal menu principale di Visual Studio scegliere **Strumenti**,  > **Gestione pacchetti libreria** > **Console di Gestione pacchetti** e quindi immettere la stringa seguente nella finestra della console:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
3. Selezionare **Enter** (Invio).  
    Questa azione aggiunge un riferimento ad Azure Notification Hubs SDK usando il [pacchetto NuGet Microsoft.Azure.NotificationHubs].

4. Aprire il file Program.cs e aggiungere l'istruzione `using` seguente:
   
        using Microsoft.Azure.NotificationHubs;

5. Nella classe `Program` aggiungere il metodo seguente oppure sostituirlo se esiste già:
   
        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub =
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
   
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business",
                                            "Technology", "Science", "Sports"};
   
            // Send the notification as a template notification. All template registrations that contain
            // "messageParam" and the proper tags will receive the notifications.
            // This includes APNS, GCM, WNS, and MPNS template registrations.
   
            Dictionary<string, string> templateParams = new Dictionary<string, string>();
   
            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
         }
   
    Questo codice invia una notifica modello per ognuno dei sei tag nella matrice di stringhe. L'uso di tag garantisce che i dispositivi ricevano le notifiche solo per le categorie registrate.

5. Nel codice precedente sostituire i segnaposto `<hub name>` e `<connection string with full access>` con il nome dell'hub di notifica e la stringa di connessione per *DefaultFullSharedAccessSignature* dal dashboard dell'hub di notifica.

6. Nel metodo **Main** aggiungere le righe seguenti:
   
         SendTemplateNotificationAsync();
         Console.ReadLine();

7. Creare l'app console.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Get started with Notification Hubs]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[interfaccia REST di Hub di notifica]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Add push notifications for Mobile Apps]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[How to use Notification Hubs from Java or PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md (Come usare Hub di notifica da Java o PHP)
[pacchetto NuGet Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/
