<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Windows Azure .Net runtime mobile services and Notification Hubs to send push notifications to your Windows phone app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc"  solutions="" writer="wesmc" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Introduzione alle notifiche push in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

Questo argomento illustra come usare Servizi mobili di Azure con un back-end .NET per inviare notifiche push a un'app per Windows Phone Silverlight 8. In questa esercitazione si utilizzerà Hub di notifica di Azure per abilitare le notifiche push nel progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push utilizzando Hub di notifica ogni volta che viene inserito un record. L'hub di notifica creato può essere utilizzato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere utilizzato da altri servizi e applicazioni.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Aggiornamento dell'app per la registrazione per le notifiche][Aggiornamento dell'app per la registrazione per le notifiche]
2.  [Aggiornare il server per l'invio di notifiche push][Aggiornare il server per l'invio di notifiche push]
3.  [Abilitare le notifiche push per test locali][Abilitare le notifiche push per test locali]
4.  [Inserimento di dati per la ricezione di notifiche push][Inserimento di dati per la ricezione di notifiche push]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili][Introduzione a Servizi mobili] o [Introduzione ai dati][Introduzione ai dati] per collegare il progetto al servizio mobile.

> [WACOM.NOTE]Questa esercitazione è destinata alle app per Windows Phone 8.1 "Silverlight". Se invece si intende creare un'app di Windows Phone 8.1 Store, vedere la versione dell'esercitazione per l' [app di Windows Store][app di Windows Store]. Per informazioni sulle app per Windows Phone Silverlight e sul confronto di tali app con quelle di Windows Phone Store, vedere l'argomento relativo alle [app per Windows Phone Silverlight 8.1][app per Windows Phone Silverlight 8.1].

## <span id="update-app"></span></a> Aggiornamento dell'app per la registrazione per le notifiche

Prima che l'app possa ricevere notifiche push, è necessario registrare un canale di notifica.

1.  In Visual Studio aprire il file App.xaml.cs e aggiungere l'istruzione `using` seguente:

        using Microsoft.Phone.Notification;

2.  Aggiungere il metodo `AcquirePushChannel` seguente per la classe `App`:

        public static HttpNotificationChannel CurrentChannel { get; private set; }  

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

    ![][0]

    In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup.

## <span id="update-server"></span></a>Aggiornare il server per l'invio di notifiche push

1.  In Esplora soluzioni di Visual Studio espandere la cartella **Controllers** nel progetto del servizio mobile. Aprire TodoItemController.cs e aggiornare la definizione del metodo `PostTodoItem` con il codice seguente:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            MpnsPushMessage message = new MpnsPushMessage();
            message.XmlPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>" + item.Text + "</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Questo codice invierà una notifica push (con il testo dell'elemento inserito) dopo l'inserimento di un elemento Todo. In caso di errore, il codice aggiungerà una voce di log errori visualizzabile nella scheda **Logs** del servizio mobile nel portale di gestione.

2.  Accedere al [portale di gestione di Azure][portale di gestione di Azure], fare clic su **Mobile Services** e quindi sull'app.

3.  Fare clic sulla scheda **Push**, selezionare **Enable unauthenticated push notifications**, quindi fare clic su **Save**.

    ![][1]

    > [WACOM.NOTE]In questa esercitazione verrà utilizzato il Servizio di notifica Push di Microsoft in modalità senza autenticazione. In tale modalità, il Servizio di notifica Push di Microsoft limita il numero di notifiche che è possibile inviare a un canale di dispositivo. Per rimuovere questa restrizione è necessario generare e caricare un certificato facendo clic su **Upload** e selezionando il certificato. Per ulteriori informazioni sulla generazione del certificato, vedere [Configurazione di un servizio Web autenticato per l'invio di notifiche push per Windows Phone][Configurazione di un servizio Web autenticato per l'invio di notifiche push per Windows Phone].

In questo modo il servizio mobile verrà abilitato per la connessione al Servizio di notifica Push di Microsof in modalità senza autenticazione per l'invio di notifiche push.

## <span id="local-testing"></span></a> Abilitare le notifiche push per test locali

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <span id="test"></span></a>Esecuzione del test delle notifiche push nell'app

1.  In Visual Studio premere F5 per eseguire l'app.

    > [WACOM.NOTE] Durante il test sull'emulatore di Windows Phone è possibile che si verifichi un'eccezione RegistrationAuthorizationException e che venga restituita una risposta 401 (Non autorizzato). Questa situazione può verificarsi durante la chiamata `RegisterNativeAsync()` a causa della modalità errata con cui l'orologio dell'emulatore di Windows Phone viene sincronizzato con il PC host. Di conseguenza, è possibile che un token di sicurezza venga rifiutato. Per risolvere questo problema, è sufficiente impostare manualmente l'orologio prima del test.

2.  Nell'app immettere il testo "hello push" nella casella di testo, fare clic su **Salva**, quindi fare immediatamente clic sul pulsante Avvia o Indietro per chiudere l'app.

    ![][2]

    Al servizio mobile verrà inviata una richiesta insert per archiviare l'elemento aggiunto. Si noti che il dispositivo riceve una notifica di tipo avviso popup con il testo **hello push**.

    ![][3]

    > [WACOM.NOTE]La notifica non viene ricevuta se l'app è ancora aperta. Per ricevere una notifica di tipo avviso popup mentre l'app è attiva, è necessario gestire l'evento [ShellToastNotificationReceived][ShellToastNotificationReceived].

## <a name="next-steps">Passaggi successivi</a>

Questa esercitazione ha illustrato le nozioni di base per consentire a un'app per Windows Phone di usare Servizi mobili e Hub di notifica per l'invio di notifiche push. In seguito, è consigliabile eseguire l'esercitazione seguente, [Invio di notifiche push agli utenti autenticati][Invio di notifiche push agli utenti autenticati], che mostra come usare i tag per inviare notifiche push da un servizio mobile solo agli utenti autenticati.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in. -->

Per altre informazioni, vedere anche i seguenti argomenti su Servizi mobili e Hub di notifica:

-   [Introduzione ai dati][Introduzione ai dati]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

-   [Informazioni su Hub di notifica][Informazioni su Hub di notifica]
    Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][Riferimento per i concetti e le procedure di Servizi mobili con .NET]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.



  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [Aggiornamento dell'app per la registrazione per le notifiche]: #update-app
  [Aggiornare il server per l'invio di notifiche push]: #update-server
  [Abilitare le notifiche push per test locali]: #local-testing
  [Inserimento di dati per la ricezione di notifiche push]: #test
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [app di Windows Store]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [app per Windows Phone Silverlight 8.1]: http://msdn.microsoft.com/it-it/library/windowsphone/develop/dn642082(v=vs.105).aspx
  [0]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png
  [Configurazione di un servizio Web autenticato per l'invio di notifiche push per Windows Phone]: http://msdn.microsoft.com/it-it/library/windowsphone/develop/ff941099(v=vs.105).aspx
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
  [ShellToastNotificationReceived]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx
  [Invio di notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users/
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/documentation/articles/mobile-services-html-how-to-use-client-library
