
Questa sezione mostra come inviare le ultime notizie come notifiche modello con tag da un'app console .NET e di qualsiasi altro tipo.

Se si usano le app per dispositivi mobili, vedere l'esercitazione [Aggiungere notifiche push alle app per dispositivi mobili] e selezionare la piattaforma in uso nella parte superiore.

Se si preferisce usare Java o PHP, vedere [Come usare Hub di notifica da Java/PHP]. È possibile inviare notifiche da qualsiasi back-end tramite l'[interfaccia REST degli Hub di notifica].

Se è stata creata l'app console per l’invio di notifiche quando è stata completata l'esercitazione [Introduzione agli Hub di notifica], ignorare i passaggi da 1 a 3.

1. In Visual Studio creare una nuova applicazione console in Visual C#:
   
       ![][13]
2. Nel menu principale di Visual Studio fare clic su **Strumenti**, **Gestione pacchetti libreria**, e **Console di Gestione pacchetti**, quindi nella finestra di tipo console digitare quanto segue e premere **INVIO**:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Verrà aggiunto un riferimento all’SDK dell’Hub di notifica di Azure mediante il [pacchetto NuGet Microsoft.Azure.NotificationHubs].
3. Aprire il file Program.cs e aggiungere l'istruzione `using` seguente:
   
        using Microsoft.Azure.NotificationHubs;
4. Nella classe `Program` aggiungere il metodo seguente oppure sostituirlo se esiste già:
   
        private static async void SendTemplateNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub =
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");
   
            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business",
                                            "Technology", "Science", "Sports"};
   
            // Sending the notification as a template notification. All template registrations that contain
            // "messageParam" and the proper tags will receive the notifications.
            // This includes APNS, GCM, WNS, and MPNS template registrations.
   
            Dictionary<string, string> templateParams = new Dictionary<string, string>();
   
            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
         }
   
    Questo codice invia una notifica modello per ognuno dei sei tag nella matrice di stringhe. Con i tag è possibile assicurarsi che i dispositivi ricevano notifiche solo per le categorie registrate.
5. Nel codice precedente sostituire i segnaposto `<hub name>` e `<connection string with full access>` con il nome dell'hub di notifica e la stringa di connessione per *DefaultFullSharedAccessSignature* dal dashboard dell'hub di notifica.
6. Aggiungere le righe seguenti nel metodo **Main** :
   
         SendTemplateNotificationAsync();
         Console.ReadLine();
7. Creare l'app console.

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

<!-- URLs. -->
[Introduzione agli Hub di notifica]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md
[interfaccia REST degli Hub di notifica]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx
[Aggiungere notifiche push alle app per dispositivi mobili]: ../articles/app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md
[Come usare Hub di notifica da Java/PHP]: ../articles/notification-hubs/notification-hubs-java-push-notification-tutorial.md
[pacchetto NuGet Microsoft.Azure.NotificationHubs]: http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/


<!--HONumber=Nov16_HO3-->


