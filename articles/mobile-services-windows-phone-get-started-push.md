<properties pageTitle="Get started with push notifications (legacy push) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Introduzione alle notifiche push in Servizi mobili (push legacy)

<div class="dev-center-tutorial-selector sublanding">

[Windows Store C#][Windows Store C#] [Windows Store JavaScript][Windows Store JavaScript] [Windows Phone][Windows Phone] [iOS][iOS] [Android][Android] <!--    <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>     <a href="/it-it/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    --> [Appcelerator][Appcelerator]

</div>

<div class="dev-center-tutorial-subselector">

[Back-end .NET][Back-end .NET] | [Back-end JavaScript][Back-end JavaScript]

</div>

Questo argomento illustra come usare Servizi mobili di Azure per inviare notifiche push a un'app per Windows Phone 8.
Questa esercitazione consente di aggiungere le notifiche push mediante il Servizio di notifica Push di Microsoft (MPNS) al progetto di guida introduttiva. Al termine dell'esercitazione, il servizio mobile invierà una notifica push ogni volta che viene inserito un record.

> [WACOM.NOTE]Questo argomento riguarda i servizi mobili *esistenti* che *non sono stati ancora aggiornati* per l'integrazione di Hub di notifica. Quando si crea un *nuovo* servizio mobile, questa funzionalità integrata viene abilitata automaticamente. Per i nuovi servizi mobili, vedere [Introduzione alle notifiche push][Introduzione alle notifiche push].
>
> Servizi mobili è integrato con Hub di notifica di Azure per supportare altre funzionalità di notifica push, ad esempio modelli, più piattaforme e maggiore scalabilità. *È consigliabile aggiornare i servizi mobili esistenti per l'uso di Hub di notifica, se possibile*. Una volta eseguito l'aggiornamento, vedere questa versione di [Introduzione alle notifiche push][Introduzione alle notifiche push].

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Creazione della tabella Registrations][Creazione della tabella Registrations]
2.  [Aggiunta di notifiche push all'app][Aggiunta di notifiche push all'app]
3.  [Aggiornamento degli script per l'invio di notifiche push][Aggiornamento degli script per l'invio di notifiche push]
4.  [Inserimento di dati per la ricezione di notifiche][Inserimento di dati per la ricezione di notifiche]

Per completare questa esercitazione, è necessario disporre di [Visual Studio 2012 Express per Windows Phone][Visual Studio 2012 Express per Windows Phone] o versione successiva.

Questa esercitazione è basata sul progetto di guida introduttiva per Servizi mobili. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione a Servizi mobili][Introduzione a Servizi mobili].

> [WACOM.NOTE]Quando si inviano ogni giorno più di 500 messaggi per ogni utente, è necessario utilizzare Hub di notifica. Per ulteriori informazioni, vedere [Introduzione ad Hub di notifica][Introduzione ad Hub di notifica].

## <a name="create-table"></a>Creazione di una nuova tabella

[WACOM.INCLUDE [mobile-services-create-new-push-table][mobile-services-create-new-push-table]]

## <a name="add-push"></a><span class="short-header">Aggiunta di notifiche push</span>Aggiunta di notifiche push all'app

1.  In Visual Studio aprire il file di progetto MainPage.xaml.cs e aggiungere il codice seguente per creare una nuova classe **Registrations**:

        public class Registrations
        {
            public string Id { get; set; }

            [JsonProperty(PropertyName = "handle")]
            public string Handle { get; set; }
        }

    La proprietà Handle viene utilizzata per archiviare l'URI di canale.

2.  Aprire il file App.xaml.cs e aggiungere l'istruzione using seguente:

        using Microsoft.Phone.Notification;

3.  Aggiungere il codice seguente al file App.xaml.cs:

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private async void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }

           IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
           var registration = new Registrations { Handle = CurrentChannel.ChannelUri.AbsoluteUri };
           await registrationsTable.InsertAsync(registration);
        }

    Questo codice consente di acquisire e archiviare un canale per una sottoscrizione di notifica push e lo associa al riquadro predefinito dell'app.

    <div class="dev-callout">

    **Nota**
    In questa esercitazione il servizio mobile invia una notifica di tipo riquadro capovolto al dispositivo. Quando si invia una notifica di tipo avviso popup, è invece necessario chiamare il metodo **BindToShellToast** sul canale. Per supportare sia la notifica di tipo riquadro che quella di tipo avviso popup, chiamare sia **BindToShellTile** che **BindToShellToast**

    </div>

4.  All'inizio del gestore dell'evento **Application\_Launching** in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo **AcquirePushChannel**:

        AcquirePushChannel();

    In tal modo si garantirà che la proprietà **CurrentChannel** venga inizializzata ogni volta che si avvia l'applicazione.

5.  In Esplora soluzioni espandere **Proprietà**, aprire il file WMAppManifest.xml, fare clic sulla scheda **Funzionalità** e assicurarsi che la funzionalità \*\*ID\_**CAP**\_PUSH\_NOTIFICATION\*\* sia selezionata.

    ![][]

    In questo modo si garantirà che l'app sia in grado di ricevere notifiche push.

## <a name="update-scripts"></a><span class="short-header">Aggiornamento dello script insert</span>Aggiornamento dello script insert registrato nel portale di gestione

[WACOM.INCLUDE [mobile-services-update-registrations-script][mobile-services-update-registrations-script]]

1.  Fare clic su **TodoItem**, quindi su **Script** e selezionare **Insert**.

    ![][1]

2.  Sostituire la funzione insert con il codice seguente, quindi fare clic su **Save**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    request.respond();
                    sendNotifications();
                }
            });

            function sendNotifications() {
                var registrationsTable = tables.getTable('Registrations');
                registrationsTable.read({
                    success: function(registrations) {
                        registrations.forEach(function(registration) {
                            push.mpns.sendFlipTile(registration.handle, {
                                title: item.text
                            }, {
                                success: function(pushResponse) {
                                    console.log("Sent push:", pushResponse);
                                }
                            });
                        });
                    }
                });
            }
        }

    Questo script insert invia una notifica push (con il testo dell'elemento inserito) a tutti i canali archiviati nella tabella **Registrations**.

## <a name="test"></a><span class="short-header">Test dell'app</span>Esecuzione del test delle notifiche push nell'app

1.  In Visual Studio scegliere **Distribuisci soluzione** dal menu **Compila**.

2.  Nell'emulatore scorrere verso sinistra per visualizzare l'elenco delle app installate e individuare la nuova app **TodoList**.

3.  Toccare e tenere premuto sull'icona dell'app, quindi scegliere **aggiungi a start** dal menu contestuale.

    ![][2]

    Al menu Start verrà aggiunto un riquadro denominato **TodoList**.

4.  Toccare il riquadro **TodoList** per avviare l'app.

    ![][3]

5.  Nell'app immettere il testo "hello push" nella casella di testo, quindi fare clic su **Save**.

    ![][4]

    Al servizio mobile verrà inviata una richiesta insert per archiviare l'elemento aggiunto.

6.  Premere **Start** per tornare al menu corrispondente.

    ![][5]

    Si noti che l'applicazione ha ricevuto la notifica push e che il titolo del riquadro è ora **hello push**.

## <a name="next-steps"> </a>Passaggi successivi

In questa esercitazione viene illustrata la funzionalità di notifica push di base fornita da Servizi mobili. Se per l'app sono richieste funzionalità più avanzate, ad esempio l'invio di notifiche tra piattaforme diverse, il routing basato sulla sottoscrizione o la gestione di volumi molto elevati, con il servizio mobile è consigliabile utilizzare Hub di notifica di Azure. Per ulteriori informazioni, vedere uno degli argomenti seguenti relativi a Hub di notifica:

-   [Introduzione ad Hub di notifica][6]
    Informazioni su come sfruttare Hub di notifica nell'app di Windows Store.

-   [Informazioni su Hub di notifica][Informazioni su Hub di notifica]
    Informazioni su come creare e inviare le notifiche push a utenti su più piattaforme.

-   [Invio di notifiche ai sottoscrittori][Invio di notifiche ai sottoscrittori]
    Informazioni su come gli utenti possono registrarsi e ricevere notifiche push per le categorie cui sono interessati.

<!--+ [Send notifications to users]     <br/>Learn how to send push notifications from a Mobile Service to specific users on any device.  + [Send cross-platform notifications to users]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Per ulteriori informazioni, vedere anche i seguenti argomenti su Servizi mobili:

-   [Introduzione ai dati][Introduzione ai dati]
    Ulteriori informazioni sull'archiviazione e sulle query dei dati mediante Servizi mobili.

-   [Introduzione all'autenticazione][Introduzione all'autenticazione]
    Informazioni sull'autenticazione degli utenti dell'app con l'account Windows.

-   [Riferimento per gli script del server di Servizi mobili][Riferimento per gli script del server di Servizi mobili]
    Ulteriori informazioni sulla registrazione e l'utilizzo di script del server.

-   [Riferimento per i concetti e le procedure di Servizi mobili con .NET][Riferimento per i concetti e le procedure di Servizi mobili con .NET]
    Ulteriori informazioni su come utilizzare Servizi mobili con .NET.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /it-it/documentation/articles/mobile-services-windows-store-dotnet-get-started-push "Windows Store C#"
  [Windows Store JavaScript]: /it-it/documentation/articles/mobile-services-windows-store-javascript-get-started-push "Windows Store JavaScript"
  [Windows Phone]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-push "Windows Phone"
  [iOS]: /it-it/documentation/articles/mobile-services-ios-get-started-push "iOS"
  [Android]: /it-it/documentation/articles/mobile-services-android-get-started-push "Android"
  [Appcelerator]: /it-it/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push "Appcelerator"
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ ".NET backend"
  [Back-end JavaScript]: /it-it/documentation/articles/mobile-services-windows-phone-get-started-push/ "JavaScript backend"
  [Introduzione alle notifiche push]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
  [Creazione della tabella Registrations]: #create-table
  [Aggiunta di notifiche push all'app]: #add-push
  [Aggiornamento degli script per l'invio di notifiche push]: #update-scripts
  [Inserimento di dati per la ricezione di notifiche]: #test
  [Visual Studio 2012 Express per Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started-wp8
  [Introduzione ad Hub di notifica]: /it-it/manage/services/notification-hubs/getting-started-windows-dotnet/
  [mobile-services-create-new-push-table]: ../includes/mobile-services-create-new-push-table.md
  []: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [mobile-services-update-registrations-script]: ../includes/mobile-services-update-registrations-script.md
  [1]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png
  [2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
  [3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
  [4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
  [6]: /it-it/manage/services/notification-hubs/get-started-notification-hubs-wp8/
  [Informazioni su Hub di notifica]: /it-it/develop/net/how-to-guides/service-bus-notification-hubs/
  [Invio di notifiche ai sottoscrittori]: /it-it/manage/services/notification-hubs/breaking-news-wp8/
  [Introduzione ai dati]: /it-it/develop/mobile/tutorials/get-started-with-data-wp8
  [Introduzione all'autenticazione]: /it-it/develop/mobile/tutorials/get-started-with-users-wp8
  [Riferimento per gli script del server di Servizi mobili]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Riferimento per i concetti e le procedure di Servizi mobili con .NET]: /it-it/develop/mobile/how-to-guides/work-with-net-client-library/
