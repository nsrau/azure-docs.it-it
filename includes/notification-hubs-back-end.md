Questa sezione mostra come inviare notifiche da un'app console .NET e di qualsiasi altro tipo.
Se si usa Servizi mobili, fare riferimento alle esercitazioni di [Introduzione alle notifiche push][]. Se si preferisce usare Java o PHP, fare riferimento a [Come usare Hub di notifica da Java/PHP][]. È possibile inviare notifiche da qualsiasi back-end tramite l'[interfaccia REST degli Hub di notifica][].

Il codice seguente consente di inviare notifiche a Windows Store, Windows Phone e a dispositivi iOS e Android.

Se è stata creata un'app console quando è stata completata l'esercitazione [Introduzione agli Hub di notifica][], ignorare i passaggi da 1 a 3.

1.  In Visual Studio creare una nuova applicazione console in Visual C\#:

    ![][ ]

2.  Nel menu principale di Visual Studio fare clic su **Strumenti**, **Gestione pacchetti di librerie** e **Console di Gestione pacchetti**, quindi nella finestra di tipo console digitare quanto segue e premere **INVIO**:

        Install-Package WindowsAzure.ServiceBus

    Verrà aggiunto un riferimento ad Azure Service Bus SDK con il [pacchetto NuGet WindowsAzure.ServiceBus][].

3.  Aprire il file Program.cs e aggiungere l'istruzione `using` seguente:

        using Microsoft.ServiceBus.Notifications;

4.  Nella classe `Program` aggiungere il metodo seguente oppure sostituirlo se esiste già:

        private static async void SendNotificationAsync()
        {
            // Define the notification hub.
            NotificationHubClient hub = 
                NotificationHubClient.CreateClientFromConnectionString(
                    "<connection string with full access>", "<hub name>");

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", 
                "Technology", "Science", "Sports"};

            foreach (var category in categories)
            {
                try
                {
                    // Define a Windows Store toast.
                    var wnsToast = "<toast><visual><binding template=\"ToastText01\">" 
                        + "<text id=\"1\">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";         
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

                    // Define an Android notification.
                    var notification = "{\"data\":{\"msg\":\"Breaking " + category + " News!\"}}";
                    await hub.SendGcmNativeNotificationAsync(notification, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
         }

    Con questo codice vengono inviate notifiche per ognuno dei sei tag della matrice di stringhe a Windows Store, Windows Phone e dispositivi iOS. Con i tag è possibile assicurarsi che i dispositivi ricevano notifiche solo per le categorie registrate.

    <div class="dev-callout"><strong>Nota</strong> 
    <p>Questo codice back-end supporta Windows Store, Windows Phone e client iOS e Android. I metodi Send restituiscono una risposta di errore se l'hub di notifica non &egrave; stato ancora configurato per una determinata piattaforma client. </p>
</div>

5.  Nel codice precedente sostituire i segnaposto `<hub name>` e `<connection string with full access>` con il nome dell'hub di notifica e la stringa di connessione per *DefaultFullSharedAccessSignature* ottenuta in precedenza.

6.  Aggiungere le righe seguenti nel metodo **Main**:

         SendNotificationAsync();
         Console.ReadLine();

<!-- Anchors --> <!-- Images. --> <!-- URLs. -->

  [Introduzione alle notifiche push]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/
  [Come usare Hub di notifica da Java/PHP]: /en-us/documentation/articles/notification-hubs-java-backend-how-to/
  [interfaccia REST degli Hub di notifica]: http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx
  [Introduzione agli Hub di notifica]: /en-us/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  [ ]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png
  [pacchetto NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
