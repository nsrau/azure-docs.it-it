<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-push-android" urlDisplayName="Get Started with Push" pageTitle="Get started with push (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Android .Net app." metaCanonical="" services="" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="ricksal" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal"></tags>

# Introduzione alle notifiche push in Servizi mobili

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/" title="Windows Store C#">Windows Store C#</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title="Windows Phone">Windows Phone</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/" title="iOS">iOS</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title="Android" class="current">Android</a>
<!---<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/" title=".NET backend" class="current">Back-end .NET</a> | <a href="/en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push/"  title="JavaScript backend">Back-end JavaScript</a></div>

In questo argomento viene illustrato come utilizzare Servizi mobili di Azure per inviare notifiche push a un'app per Android. L'esercitazione consente di aggiungere notifiche push al progetto di guida introduttiva tramite Google Cloud Messaging (GCM). Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

In questa esercitazione vengono descritte le operazioni seguenti:

1.  [Abilitazione di Google Cloud Messaging][]
2.  [Configurare il servizio mobile per l'invio di richieste push][]
3.  [Aggiornare il server per l'invio di notifiche push][]
4.  [Aggiungere notifiche push all'app][]
5.  [Abilitare le notifiche push per test locali][]
6.  [Eseguire il test dell'app sul servizio mobile pubblicato][]

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare l'esercitazione, è necessario completare [Introduzione a Servizi mobili][] o [Introduzione ai dati][] per collegare il progetto al servizio mobile. Di conseguenza, per completare l'esercitazione è necessario anche Visual Studio 2013.

> [WACOM.NOTE] Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][].

## <span id="register"></span></a>Abilitazione di Google Cloud Messaging

[WACOM.INCLUDE [Enable GCM][]]

## <span id="configure"></span></a>Configurazione di Servizi mobili per l'invio di richieste push

1.  Accedere al [portale di gestione di Azure][], fare clic su **Mobile Services** e quindi sull'app.

    ![][]

2.  Fare clic sulla scheda **Push** e immettere il valore **Chiave API** ottenuto da GCM nella procedura precedente, quindi fare clic su **Salva**.

    ![][1]

    <div class="dev-callout"><b>Importante</b>
<p>Le credenziali GCM per le notifiche push avanzate configurate nella scheda Push del portale vengono condivise con Hub di notifica per configurare l'hub di notifica con l'app.</p>
</div>

Il servizio mobile è ora configurato per funzionare con GCM e Hub di notifica.

## <a name="download-the-service"></a><span class="short-header">Scaricare il servizio</span>Scaricare il servizio mobile nel computer locale

[WACOM.INCLUDE [mobile-services-download-service-locally][]]

## <a name="test-the-service"></a><span class="short-header">Eseguire il test del servizio</span>Eseguire il test del servizio mobile

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service][]]

## <span id="update-server"></span></a>Aggiornare il server per l'invio di notifiche push

1.  In Esplora soluzioni di Visual Studio espandere la cartella **Controllers** nel progetto del servizio mobile. Aprire TodoItemController.cs e aggiungere l'istruzione `using` seguente all'inizio del file:

        using System;
        using System.Collections.Generic;

2.  Aggiornare la definizione del metodo `PostTodoItem` con il codice seguente:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

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

## <a name="publish-the-service"></a><span class="short-header">Pubblicare il servizio</span>Pubblicare il servizio mobile in Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service][]]

## <a name="update-app"></a>Aggiungere notifiche push all'app

### Verificare la versione di Android SDK

[WACOM.INCLUDE [mobile-services-verify-android-sdk-version][]]

Il passaggio successivo comporta l'installazione di Google Play Services. Google Cloud Messaging prevede alcuni requisiti minimi a livello di API per lo sviluppo e il testing. È necessario che la proprietà **minSdkVersion** nel file manifesto sia conforme a tali requisiti.

Se il test verrà eseguito con un dispositivo meno recente, fare riferimento alla pagina relativa alla [configurazione di Google Play Services SDK] per determinare il livello minimo su cui è possibile impostare tale valore.

### Aggiungere Google Play Services al progetto

[WACOM.INCLUDE [Add Play Services][]]

### Aggiungere codice

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push][]]

## <a name="test-app"></a><span class="short-header">Eseguire il test dell'app</span>Eseguire il test dell'app sul servizio mobile pubblicato

È possibile eseguire il test dell'app collegando direttamente un telefono Android con un cavo USB oppure utilizzando un dispositivo virtuale nell'emulatore.

### Uso dell'emulatore per il test

Assicurarsi di usare un emulatore Android Virtual Device (AVD) con il supporto per Google APIs.

1.  In **Finestra** selezionare **Android Virtual Device Manager**, quindi selezionare il dispositivo e fare clic su **Modifica** (oppure su **Nuovo** se non sono presenti dispositivi).

    ![][2]

2.  Selezionare **Google APIs** (o **Google APIs x86**) in **Destinazione**, quindi fare clic su OK.

    ![][3]

    AVD sarà quindi configurato per l'utilizzo di Google APIs. Se sono installate diverse versioni di Android SDK, assicurarsi che il livello dell'API corrisponda a quello impostato in precedenza nelle impostazioni del progetto.

### <span id="local-testing"></span></a> Abilitare le notifiche push per test locali

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][]]

### Esecuzione del test

1.  Dal menu **Run** di Eclipse, fare clic su **Run** per avviare l'app.

2.  Digitare testo significativo nell'app, ad esempio *A new Mobile Services task*, quindi fare clic sul pulsante **Add**.

    ![][4]

3.  Scorrere verso il basso dalla parte superiore della schermata per aprire il Notification Center del dispositivo e visualizzare la notifica.

L'esercitazione è stata completata.

## <a name="next-steps"> </a>Passaggi successivi

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.   + [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Per altre informazioni su Servizi mobili e su Hub di notifica, fare riferimento ai seguenti argomenti:

-   [Introduzione ai dati][]
    Altre informazioni sull'archiviazione e sulle query dei dati mediante i servizi mobili.

-   [Introduzione all'autenticazione][]
    Informazioni sull'autenticazione degli utenti dell'app con tipi di account diversi mediante i servizi mobili.

-   [Informazioni su Hub di notifica][]
    Altre informazioni sull'uso di Hub di notifica per recapitare le notifiche alle app in tutte le principali piattaforme client.

-   [Come usare la libreria client di Android per Servizi mobili][]
    Altre informazioni su come usare Servizi mobili con Android.

<!-- Anchors. --> 
<!-- Images. --> 
<!-- URLs. -->

  [Windows Store C\#]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ "Windows Store C#"
  [Windows Store JavaScript]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/ "Windows Store JavaScript"
  [Windows Phone]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ "Windows Phone"
  [iOS]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/ "iOS"
  [Android]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ "Android"
  [Back-end .NET]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/ ".NET backend"
  [Back-end JavaScript]: /en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push/ "JavaScript backend"
  [Abilitazione di Google Cloud Messaging]: #register
  [Configurare il servizio mobile per l'invio di richieste push]: #configure
  [Aggiornare il server per l'invio di notifiche push]: #update-server
  [Aggiungere notifiche push all'app]: #update
  [Abilitare le notifiche push per test locali]: #local-testing
  [Eseguire il test dell'app sul servizio mobile pubblicato]: #test-app
  [Introduzione a Servizi mobili]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started
  [Introduzione ai dati]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-data
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [Enable GCM]: ../includes/mobile-services-enable-Google-cloud-messaging.md
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  []: ./media/mobile-services-android-get-started-push/mobile-services-selection.png
  [1]: ./media/mobile-services-android-get-started-push/mobile-push-tab-android.png
  [mobile-services-download-service-locally]: ../includes/mobile-services-download-service-locally.md
  [mobile-services-dotnet-backend-test-local-service]: ../includes/mobile-services-dotnet-backend-test-local-service.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [mobile-services-verify-android-sdk-version]: ../includes/mobile-services-verify-android-sdk-version.md
  [Add Play Services]: ../includes/mobile-services-add-Google-play-services.md
  [mobile-services-android-getting-started-with-push]: ../includes/mobile-services-android-getting-started-with-push.md
  [2]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png
  [3]: ./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [4]: ./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png
  [Introduzione all'autenticazione]: /en-us/documentation/articles/mobile-services-dotnet-backend-android-get-started-users
  [Informazioni su Hub di notifica]: /en-us/documentation/articles/notification-hubs-overview/
  [Come usare la libreria client di Android per Servizi mobili]: /en-us/documentation/articles/mobile-services-android-how-to-use-client-library
