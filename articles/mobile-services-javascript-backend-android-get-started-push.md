<properties linkid="develop-mobile-tutorials-get-started-with-push-js-vs2013" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (Android JavaScript) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android JavaScript app." metaCanonical="http://www.windowsazure.com/it-it/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal"  solutions="" writer="ricksal" manager="" editor=""   />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# <a name="getting-started-with-push"> </a>Introduzione alle notifiche push in Servizi mobili

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push" title="Windows Store C#">Windows Store C#</a><a href="/it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/mobile-services-javascript-backend-ios-get-started-push" title="iOS">iOS</a><a href="/it-it/documentation/articles/mobile-services-javascript-backend-android-get-started-push" title="Android" class="current">Android</a>
<!-- <a href="/it-it/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET back-end" >Back-end .NET</a> | <a href="/it-it/documentation/articles/mobile-services-javascript-backend-android-get-started-push/" title="JavaScript back-end" class="current">Back-end JavaScript</a></div>

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per inviare notifiche push a un'app per Android. L'esercitazione consente di aggiungere notifiche push al progetto di guida introduttiva tramite Google Cloud Messaging (GCM). Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

> [WACOM.NOTE]Questa esercitazione illustra l'integrazione di Servizi mobili con Hub di notifica, l'infrastruttura che consente l'invio di notifiche push dal servizio mobile. Se si usa un servizio mobile meno recente che fa uso del push legacy e non è stato ancora aggiornato per Hub di notifica, *è consigliabile eseguire l'aggiornamento* nel corso di questa esercitazione. Se si sceglie di non eseguire ora l'aggiornamento, seguire questa versione dell'esercitazione: [Introduzione alle notifiche push (legacy)][].

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Abilitazione di Google Cloud Messaging][]
2.  [Configurazione di Servizi mobili][]
3.  [Aggiungere notifiche push all'app][]
4.  [Aggiornamento degli script per l'invio di notifiche push][]
5.  [Inserimento di dati per la ricezione di notifiche][]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili][] o [Introduzione ai dati][] per collegare il progetto al servizio mobile.

## <span id="register"></span></a>Abilitazione di Google Cloud Messaging

> [WACOM.NOTE]Per completare questa procedura, è necessario disporre di un account Google con un indirizzo di posta elettronica verificato. Per creare un nuovo account Google, visitare il sito Web all'indirizzo [accounts.google.com][].

[WACOM.INCLUDE [Enable GCM][]]

Questo valore della chiave dell'API verrà usato successivamente per abilitare Servizi mobili per l'autenticazione con GCM e l'invio di notifiche push per conto dell'app.

## <span id="configure"></span></a>Configurazione di Servizi mobili per l'invio di richieste push

1.  Accedere al [portale di gestione di Azure][], fare clic su **Mobile Services** e quindi sull'app.

    ![][]

2.  Fare clic sulla scheda **Push**, immettere il valore **Chiave API** ottenuto da GCM nella procedura precedente, quindi fare clic su **Salva**.

    > [WACOM.NOTE]Quando si completa questa esercitazione usando un servizio mobile meno recente, è possibile che nella parte inferiore della scheda **Push** venga visualizzato un collegamento **Abilita push avanzato**. Fare clic su questo collegamento per aggiornare il servizio mobile per l'integrazione con Hub di notifica. Questa modifica non può essere annullata. Per informazioni dettagliate su come abilitare le notifiche push avanzate in un servizio mobile di produzione, vedere [queste linee guida][].

    ![][1]

    <div class="dev-callout"><b>Importante</b>
<p>Le credenziali GCM per le notifiche push avanzate configurate nella scheda Push del portale vengono condivise con Hub di notifica per configurare l'hub di notifica con l'app.</p>
</div>

Il servizio mobile e l'app sono ora configurati per funzionare con GCM e Hub di notifica.

## <span id="add-push"></span></a>Aggiungere notifiche push all'app

### Verificare la versione di Android SDK

[WACOM.INCLUDE [Verifica SDK][]]

Il passaggio successivo comporta l'installazione di Google Play Services. Google Cloud Messaging prevede alcuni requisiti minimi a livello di API per lo sviluppo e il testing. È necessario che la proprietà **minSdkVersion** nel file manifesto sia conforme a tali requisiti.

Se il test verrà eseguito con un dispositivo meno recente, fare riferimento alla pagina relativa alla [configurazione di Google Play Services SDK][] per determinare il livello minimo su cui è possibile impostare tale valore.

### Aggiungere Google Play Services al progetto

[WACOM.INCLUDE [Add Play Services][]]

### Aggiungere codice

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push][]]

## <span id="update-scripts"></span></a>Aggiornamento dello script insert registrato nel portale di gestione

1.  Nel portale di gestione fare clic sulla scheda **Data** e quindi sulla tabella **TodoItem**.

    ![][2]

2.  In **todoitem** fare clic sulla scheda **Script**, quindi selezionare **Insert**.

    ![][3]

    Verrà visualizzata la funzione che viene richiamata quando si verifica un inserimento nella tabella **TodoItem**.

3.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

        function insert(item, user, request) {
        // Define a payload for the Google Cloud Messaging toast notification.
        var payload = 
            '{"data":{"message" : ' + item.text + ' }}';

        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.gcm.send(null, payload, {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse, payload);
                        request.respond();
                        },              
                    error: function (pushResponse) {
                        console.log("Error Sending push:", pushResponse);
                        request.respond(500, { error: pushResponse });
                        }
                    });
                },
            error: function(err) {
                console.log("request.execute error", err)
                request.respond();
            }
          });
        }

    Verrà registrato un nuovo script insert, che usa l'[oggetto gcm][] per inviare una notifica push a tutti i dispositivi registrati dopo la corretta esecuzione dell'inserimento.

## <span id="test"></span></a>Esecuzione del test delle notifiche push nell'app

È possibile eseguire il test dell'app collegando direttamente un telefono Android con un cavo USB oppure utilizzando un dispositivo virtuale nell'emulatore.

### Configurazione dell'emulatore per il test

Quando si esegue l'app nell'emulatore, assicurarsi di utilizzare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

1.  Riavviare Eclipse, quindi in Package Explorer fare clic con il pulsante destro del mouse sul progetto, scegliere **Properties**, fare clic su **Android**, selezionare **Google APIs** e infine fare clic su **OK**.

    ![][4]

    Il progetto farà quindi riferimento a Google APIs.

2.  Da **Window** selezionare **Android Virtual Device Manager**, quindi selezionare il dispositivo e fare clic su **Edit**.

    ![][5]

3.  Selezionare **Google APIs** in **Target**, quindi fare clic su OK.

    ![][6]

    AVD sarà quindi configurato per l'utilizzo di Google APIs.

### Esecuzione del test

1.  Dal menu **Run** di Eclipse, fare clic su **Run** per avviare l'app.

2.  Digitare testo significativo nell'app, ad esempio *A new Mobile Services task*, quindi fare clic sul pulsante **Add**.

    ![][7]

3.  Scorrere verso il basso dalla parte superiore della schermata per aprire il Notification Center del dispositivo e visualizzare la notifica.

L'esercitazione è stata completata.

## <a name="next-steps"> </a>Passaggi successivi

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

-   [Introduzione ai dati][]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione][]
    Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

-   [Informazioni su Hub di notifica][]
    Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

-   [Come usare la libreria client di Android per Servizi mobili][]
    Altre informazioni su come usare Servizi mobili con Android.

-   [Riferimento per gli script server di Servizi mobili][]
    Ulteriori informazioni su come implementare la logica di business nel servizio mobile.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Windows Store C#"
  [Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Windows Store JavaScript"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone"
  [iOS]: /it-it/documentation/articles/mobile-services-javascript-backend-ios-get-started-push "iOS"
  [Android]: /it-it/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android"
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ ".NET back-end"
  [Back-end JavaScript]: /it-it/documentation/articles/mobile-services-javascript-backend-android-get-started-push/ "JavaScript back-end"
  [Introduzione alle notifiche push (legacy)]: /it-it/documentation/articles/mobile-services-android-get-started-push/
  [Abilitazione di Google Cloud Messaging]: #register
  [Configurazione di Servizi mobili]: #configure
  [Aggiungere notifiche push all'app]: #add-push
  [Aggiornamento degli script per l'invio di notifiche push]: #update-scripts
  [Inserimento di dati per la ricezione di notifiche]: #test
  [Introduzione a Servizi mobili]: /it-it/documentation/articles/mobile-services-android-get-started/
  [Introduzione ai dati]: /it-it/documentation/articles/mobile-services-android-get-started-data/
  [accounts.google.com]: http://go.microsoft.com/fwlink/p/?LinkId=268302
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [queste linee guida]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [Verifica SDK]: ../includes/mobile-services-verify-android-sdk-version.md
  [configurazione di Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
  [Add Play Services]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png
  [oggetto gcm]: http://go.microsoft.com/fwlink/p/?LinkId=282645
  [4]: ./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png
  [5]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [6]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [7]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [Introduzione all'autenticazione]: /it-it/documentation/articles/mobile-services-android-get-started-users
  [Informazioni su Hub di notifica]: /it-it/documentation/articles/notification-hubs-overview/
  [Come usare la libreria client di Android per Servizi mobili]: /it-it/documentation/articles/mobile-services-android-how-to-use-client-library
  [Riferimento per gli script server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
