<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# Introduzione alle notifiche push in Servizi mobili

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy][]]

Questo argomento illustra come usare Servizi mobili di Azure per inviare notifiche push a un'app per Windows Phone Silverlight. In questa esercitazione si utilizzerà Hub di notifica di Azure per abilitare le notifiche push nel progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push utilizzando Hub di notifica ogni volta che viene inserito un record. L'hub di notifica creato può essere utilizzato gratuitamente con il servizio mobile, può essere gestito indipendentemente da quest'ultimo e può essere utilizzato da altri servizi e applicazioni.

> [WACOM.NOTE]Questa esercitazione illustra l'integrazione di Servizi mobili con Hub di notifica, l'infrastruttura che consente l'invio di notifiche push dal servizio mobile. Se si usa un servizio mobile meno recente che fa uso del push legacy e non è stato ancora aggiornato per Hub di notifica, *è consigliabile eseguire l'aggiornamento* nel corso di questa esercitazione. Se si sceglie di non eseguire ora l'aggiornamento, seguire questa versione dell'esercitazione: [Introduzione alle notifiche push (legacy)][].

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Aggiornamento dell'app per la registrazione per le notifiche][]
2.  [Aggiornamento degli script del server per l'invio di notifiche push][]
3.  [Inserimento di dati per la ricezione di notifiche push][]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili][] o [Introduzione ai dati][] per collegare il progetto al servizio mobile. Se non è stato collegato alcun servizio mobile, la procedura guidata Aggiungi notifica push crea automaticamente la connessione.

> [WACOM.NOTE]Per inviare notifiche push a un'app di Windows Phone 8.1 Store, vedere la versione dell'esercitazione per l' [app di Windows Store][].

## <span id="update-app"></span></a> Aggiornamento dell'app per la registrazione per le notifiche

Prima che l'app possa ricevere notifiche push, è necessario registrare un canale di notifica.

1.  In Visual Studio aprire il file App.xaml.cs e aggiungere l'istruzione `using` seguente:

        using Microsoft.Phone.Notification;

2.  Aggiungere il codice seguente al file App.xaml.cs:

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
                    await MobileService.GetPush()
                        .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                });
        }

    Questo codice consente di recuperare il valore di ChannelURI per l'app dal Servizio di notifica Push di Microsoft (MPNS) usato da Windows Phone 8.x "Silverlight", quindi di registrarlo per le notifiche push.

    > [WACOM.NOTE]In questa esercitazione il servizio mobile invia una notifica di tipo avviso popup al dispositivo. Quando si invia una notifica di tipo riquadro, è invece necessario chiamare il metodo **BindToShellTile** sul canale.

3.  All'inizio del gestore dell'evento **Application\_Launching** in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo **AcquirePushChannel**:

        AcquirePushChannel();

    In questo modo la registrazione verrà richiesta ogni volta che viene caricata la pagina. Nell'app dell'utente può essere preferibile eseguire la registrazione solo su base periodica, per verificare che sia aggiornata.

4.  Premere **F5** per eseguire l'app. Verrà visualizzata una finestra di dialogo popup con la chiave di registrazione.

5.  In Esplora soluzioni espandere **Proprietà**, aprire il file WMAppManifest.xml, fare clic sulla scheda **Funzionalità** e assicurarsi che la funzionalità \*\*ID\_**CAP**\_PUSH\_NOTIFICATION\*\* sia selezionata.

    ![][]

    In questo modo si garantirà che l'app sia in grado di generare notifiche di tipo avviso popup.

## <span id="update-scripts"></span></a> Aggiornamento degli script del server per l'invio di notifiche push

È infine necessario aggiornare lo script registrato nell'operazione di inserimento nella tabella TodoItem per l'invio di notifiche.

1.  Fare clic su **TodoItem**, quindi su **Script** e selezionare **Insert**.

    ![][1]

2.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

        function insert(item, user, request) {
        // Define a payload for the Windows Phone toast notification.
        var payload = '<?xml version="1.0" encoding="utf-8"?>' +
            '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
            '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
            '</wp:Text2></wp:Toast></wp:Notification>';

        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.mpns.send(null, payload, 'toast', 22, {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse);
                        request.respond();
                        },              
                        error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
                            request.respond(500, { error: pushResponse });
                            }
                        });
                    }
                });      
        }

    Questo script insert invia una notifica push (con il testo dell'elemento inserito) a tutte le app per Windows Phone registrate dopo la corretta esecuzione dell'inserimento.

3.  Fare clic sulla scheda **Push**, selezionare **Enable unauthenticated push notifications**, quindi fare clic su **Save**.

    > [WACOM.NOTE]Quando si completa questa esercitazione usando un servizio mobile meno recente, è possibile che nella parte inferiore della scheda **Push** venga visualizzato un collegamento **Abilita push avanzato**. Fare clic su questo collegamento per aggiornare il servizio mobile per l'integrazione con Hub di notifica. Questa modifica non può essere annullata. Per informazioni dettagliate su come abilitare le notifiche push avanzate in un servizio mobile di produzione, vedere [queste linee guida][].

    ![][2]

    In questo modo il servizio mobile verrà abilitato per la connessione al Servizio di notifica Push di Microsof in modalità senza autenticazione per l'invio di notifiche push.

    > [WACOM.NOTE]In questa esercitazione verrà utilizzato il Servizio di notifica Push di Microsoft in modalità senza autenticazione. In tale modalità, il Servizio di notifica Push di Microsoft limita il numero di notifiche che è possibile inviare a un canale di dispositivo. Per rimuovere questa restrizione è necessario generare e caricare un certificato facendo clic su **Upload** e selezionando il certificato. Per ulteriori informazioni sulla generazione del certificato, vedere [Configurazione di un servizio Web autenticato per l'invio di notifiche push per Windows Phone][].

## <span id="test"></span></a>Esecuzione del test delle notifiche push nell'app

1.  In Visual Studio premere F5 per eseguire l'app.

    > [WACOM.NOTE] Durante il test sull'emulatore di Windows Phone è possibile che si verifichi un'eccezione RegistrationAuthorizationException e che venga restituita una risposta 401 (Non autorizzato). Questa situazione può verificarsi durante la chiamata `RegisterNativeAsync()` a causa della modalità errata con cui l'orologio dell'emulatore di Windows Phone viene sincronizzato con il PC host. Di conseguenza, è possibile che un token di sicurezza venga rifiutato. Per risolvere questo problema, è sufficiente impostare manualmente l'orologio prima del test.

2.  Nell'app immettere il testo "hello push" nella casella di testo, fare clic su **Salva**, quindi fare immediatamente clic sul pulsante Avvia o Indietro per chiudere l'app.

    ![][3]

    Al servizio mobile verrà inviata una richiesta insert per archiviare l'elemento aggiunto. Si noti che il dispositivo riceve una notifica di tipo avviso popup con il testo **hello push**.

    ![][4]

    > [WACOM.NOTE]La notifica non viene ricevuta se l'app è ancora aperta. Per ricevere una notifica di tipo avviso popup mentre l'app è attiva, è necessario gestire l'evento [ShellToastNotificationReceived][].

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione sono state illustrate le nozioni di base per consentire a un'app di Windows Store di usare Servizi mobili e Hub di notifica per l'invio di notifiche push. In seguito, è consigliabile eseguire una delle esercitazioni seguenti:

-   [Invio di notifiche push agli utenti autenticati][]
    Informazioni sull'uso dei tag per inviare notifiche push da un servizio mobile solo agli utenti autenticati.

-   [Invio di notifiche di trasmissione ai sottoscrittori][]
    Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

<!---+ [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

-   [Introduzione ai dati][]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione][]
    Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

-   [Informazioni su Hub di notifica][]
    Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.

-   [Riferimento per gli script server di Servizi mobili][]
    Ulteriori informazioni su come implementare la logica di business nel servizio mobile.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [Introduzione alle notifiche push (legacy)]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-push/
  [Aggiornamento dell'app per la registrazione per le notifiche]: #update-app
  [Aggiornamento degli script del server per l'invio di notifiche push]: #update-scripts
  [Inserimento di dati per la ricezione di notifiche push]: #test
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-windows-phone-get-started
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-data
  [app di Windows Store]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
  []: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
  [queste linee guida]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png
  [Configurazione di un servizio Web autenticato per l'invio di notifiche push per Windows Phone]: http://msdn.microsoft.com/it-it/library/windowsphone/develop/ff941099(v=vs.105).aspx
  [3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
  [ShellToastNotificationReceived]: http://msdn.microsoft.com/it-it/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx
  [Invio di notifiche push agli utenti autenticati]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/
  [Invio di notifiche di trasmissione ai sottoscrittori]: /it-it/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-users
  [Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Riferimento per gli script server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
