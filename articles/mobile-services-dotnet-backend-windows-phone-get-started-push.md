<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Windows Azure .Net runtime mobile services and Notification Hubs to send push notifications to your Windows phone app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" writer="wesmc" manager="" editor="" />

Introduzione alle notifiche push in Servizi mobili
==================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push "Windows Store C#") [Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push "Windows Store JavaScript") [Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push "Windows Phone")
[Back-end .NET](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push "Back-end .NET") | [Back-end JavaScript](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/ "Back-end JavaScript")

In questo argomento viene illustrato come utilizzare i servizi mobili di runtime .NET di Azure per inviare notifiche push a un'app per Windows Phone 8. In questa esercitazione si utilizzerà Hub di notifica di Azure per abilitare le notifiche push nel progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile di runtime .NET invierà una notifica push utilizzando Hub di notifica ogni volta che viene inserito un record. L'hub di notifica creato può essere utilizzato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere utilizzato da altri servizi e applicazioni.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Aggiornamento dell'app per la registrazione per le notifiche](#update-app)
2.  [Aggiornamento del server per l'invio di notifiche push](#update-server)
3.  [Inserimento di dati per la ricezione di notifiche push](#test)

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili](/en-us/documentation/articles/mobile-services-windows-store-get-started) o [Introduzione ai dati](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data) per collegare il progetto al servizio mobile.

Aggiornamento dell'app per la registrazione per le notifiche
------------------------------------------------------------

Prima che l'app possa ricevere notifiche push, è necessario registrare un canale di notifica.

1.  In Visual Studio aprire il file App.xaml.cs e aggiungere le istruzioni `using` seguenti:

         using Microsoft.Phone.Notification;

2.  Aggiungere il metodo `AcquirePushChannel` seguente alla classe `App`:

         private void AcquirePushChannel()
         {
             CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");
             if (CurrentChannel == null)
             {
                 CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                 CurrentChannel.Open();
                 CurrentChannel.BindToShellToast();
             }
             CurrentChannel.ChannelUriUpdated +=
                 new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                 {
                     // Register for notifications using the new channel
                     System.Exception exception = null;
                     try
                     {
                         await MobileService.GetPush()
                             .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                     }
                     catch (System.Exception ex)
                     {
                         CurrentChannel.Close();
                         exception = ex;
                     }
                     if (exception != null)
                     {
                         Deployment.Current.Dispatcher.BeginInvoke(() =>
                         {
                             MessageBox.Show(exception.Message, 
                                             "Registering for Push Notifications",
                                             MessageBoxButton.OK);
                         });
                     }
             });
             CurrentChannel.ShellToastNotificationReceived += 
                 new EventHandler<NotificationEventArgs>((o, args) =>
                 {
                     string message = "";
                     foreach (string key in args.Collection.Keys)
                     {
                         message += key + " : " + args.Collection[key] + ", ";
                     }
                     Deployment.Current.Dispatcher.BeginInvoke(() =>
                     {
                         MessageBox.Show(message);
                     });
             });
         }

    Questo codice recupera l'URI di canale dell'app, se esistente. In caso contrario, l'URI verrà creato. L'URI di canale verrà quindi aperto e associato alle notifiche di tipo avviso popup. Dopo la completa apertura dell'URI di canale, verrà chiamato il gestore del metodo `ChannelUriUpdated` e il canale verrà registrato per la ricezione di notifiche push. Se la registrazione ha esito negativo, il canale verrà chiuso in modo che le esecuzioni successive dell'app possano tentare nuovamente la registrazione. Il gestore `ShellToastNotificationReceived` è configurato in modo che l'app possa ricevere e gestire le notifiche push durante l'esecuzione.

3.  Nel gestore dell'evento `Application_Launching` in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo `AcquirePushChannel`:

         AcquirePushChannel();

    In questo modo la registrazione verrà richiesta ogni volta che viene caricata l'app. Nell'app dell'utente può essere preferibile eseguire la registrazione solo su base periodica, per verificare che sia aggiornata.

4.  Premere **F5** per eseguire l'app. Verrà visualizzata una finestra di dialogo popup con la chiave di registrazione.

5.  In Visual Studio aprire il file Package.appxmanifest e verificare che nella scheda **Application UI** l'opzione **Toast capable** sia impostata su **Yes**.

    ![][1]

    In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup. 

Aggiornamento del server per l'invio di notifiche push
------------------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

1.  Accedere al [portale di gestione di Azure](%20https://manage.windowsazure.com/), fare clic su **Mobile Services** e quindi sull'app.

2.  Fare clic sulla scheda **Push**, selezionare **Enable unauthenticated push notifications**, quindi fare clic su **Save**.

![](./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png)

> [WACOM.NOTE]In questa esercitazione verrà utilizzato il Servizio di notifica Push di Microsoft in modalità senza autenticazione. In tale modalità, il Servizio di notifica Push di Microsoft limita il numero di notifiche che è possibile inviare a un canale di dispositivo. Per rimuovere questa restrizione è necessario generare e caricare un certificato facendo clic su **Upload** e selezionando il certificato. Per ulteriori informazioni sulla generazione del certificato, vedere [Configurazione di un servizio Web autenticato per l'invio di notifiche push per Windows Phone](http://msdn.microsoft.com/en-us/library/windowsphone/develop/ff941099(v=vs.105).aspx).

In questo modo il servizio mobile verrà abilitato per la connessione al Servizio di notifica Push di Microsof in modalità senza autenticazione per l'invio di notifiche push.

Esecuzione del test delle notifiche push nell'app
-------------------------------------------------

1.  In Visual Studio premere F5 per eseguire l'app.

2.  Nell'app immettere il testo "hello push" nella casella di testo, quindi fare clic su **Save**.

    ![][2]

Al servizio mobile verrà inviata una richiesta insert per archiviare l'elemento aggiunto. Si noti che l'applicazione riceve una notifica di tipo avviso popup con il testo **hello push**.

Passaggi successivi
-------------------

In questa esercitazione vengono illustrate le nozioni di base per consentire a un'app di Windows Store di utilizzare dati in Servizi mobili. In seguito, è consigliabile eseguire una delle esercitazioni seguenti, basate sull'app GetStartedWithData creata in questa esercitazione:

-   [Introduzione ad Hub di notifica](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/) <br/>
    Informazioni su come sfruttare Hub di notifica nell'app di Windows Store.

-   [Invio di notifiche ai sottoscrittori](/en-us/manage/services/notification-hubs/breaking-news-dotnet/) <br/>
    Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

-   [Invio di notifiche agli utenti](/en-us/manage/services/notification-hubs/notify-users/) <br/>
    Informazioni su come inviare notifiche push da un servizio mobile a utenti specifici su qualsiasi dispositivo.

-   [Invio di notifiche su piattaforme diverse agli utenti](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/) <br/>
    Informazioni su come utilizzare i modelli per inviare notifiche push da un servizio mobile, senza che sia necessario creare payload specifici della piattaforma nel back-end.

Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data) <br/>
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante i servizi mobili di runtime .NET.

-   [Introduzione all'autenticazione](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users) <br/>
    Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili di runtime .NET.

-   [Riferimento per gli script del server di Servizi mobili](http://go.microsoft.com/fwlink/?LinkId=262293) <br/>
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET](/en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library) <br/>
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-windows-store-get-started
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push

[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/getting-started-windows-dotnet/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-dotnet/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET How-to Conceptual Reference]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
