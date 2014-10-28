<properties linkid="develop-notificationhubs-tutorials-get-started-windowsphone" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# Introduzione ad Hub di notifica

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/it-it/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/it-it/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Questo argomento descrive come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Windows Phone 8 o Windows Phone 8.1 Silverlight. Se si sviluppano app per Windows Phone 8.1 (non Silverlight), fare riferimento alla versione [Windows Universal][1].
In questa esercitazione si creerà un'app per Windows Phone 8 vuota che riceve notifiche push tramite il Servizio di notifica Push di Microsoft. Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

> [AZURE.NOTE] Hub di notifica per Windows Phone SDK non supporta l'uso di WNS con app per Windows Phone 8.1 Silverlight. Per usare WNS (invece di MPNS) con app per Windows Phone 8.1 Silverlight, è necessario configurare le credenziali WNS, come illustrato in [Introduzione a Windows Universal][1]. Sarà quindi possibile effettuare la registrazione dal back-end, come illustrato nell'esercitazione sull'[invio di notifiche agli utenti][invio di notifiche agli utenti] oppure usare le [API REST degli hub di notifica][API REST degli hub di notifica].

In questa esercitazione vengono descritte le operazioni seguenti per abilitare le notifiche push:

1.  [Creare l'hub di notifica][Creare l'hub di notifica]
2.  [Connessione dell'app all'hub di notifica][Connessione dell'app all'hub di notifica]
3.  [Inviare notifiche dal back-end][Inviare notifiche dal back-end]

In questa esercitazione viene illustrato uno scenario di trasmissione semplice tramite hub di notifica. Seguire anche l'esercitazione successiva per imparare a usare gli hub di notifica destinati a gruppi di dispositivi e a utenti specifici. Per completare questa esercitazione, è necessario disporre di:

-   [Visual Studio 2012 Express per Windows Phone][Visual Studio 2012 Express per Windows Phone] o versione successiva.

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub notifica relative ad app per Windows Phone 8.

<div class="dev-callout"><strong>Nota</strong> <p>Per completare l'esercitazione, &egrave; necessario disporre di un account Azure attivo. Se non si dispone di un account, &egrave; possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.</p></div>

## <a name="configure-hub"></a>Creare l'hub di notifica

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure] e fare clic su **+NUOVO** nella parte inferiore della schermata.

2.  Fare clic su **Servizi app**, selezionare **Bus di servizio**, **Hub di notifica**, quindi **Creazione rapida**.

    ![][]

3.  Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Create a new Notification Hub**.

    ![][2]

4.  Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic sulla scheda **Configure** nella parte superiore.

    ![][3]

5.  Fare clic sulla scheda **Notification Hubs** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

    ![][4]

6.  Fare clic su **Connection Information** nella parte inferiore. Prendere nota delle due stringhe di connessione.

    ![][5]

7.  Fare clic sulla scheda **Configure**, quindi selezionare la casella di controllo **Enable unauthenticated push notifications** nella sezione **Windows Phone notifications settings**.

    ![][6]

A questo punto si dispone delle stringhe di connessione necessarie per registrare l'app per Windows Phone 8 e inviare le notifiche.

<div class="dev-callout"><b>Nota</b>
        <p>In questa esercitazione verr&agrave; usato il Servizio di notifica Push di Microsoft in modalit&agrave; senza autenticazione. Tale modalit&agrave; prevede restrizioni in relazione alle notifiche che &egrave; possibile inviare a ciascun canale. In Hub di notifica &egrave; supportata la <a href="http://msdn.microsoft.com/it-it/library/windowsphone/develop/ff941099(v=vs.105).aspx">modalit&agrave; con autenticazione del Servizio di notifica Push di Microsoft</a>. <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.--></p>
</div>

## <a name="connecting-app"></a>Connessione dell'app all'hub di notifica

1.  In Visual Studio creare una nuova applicazione per Windows Phone 8.

    ![][7]

    In Visual Studio 2013 Update 2 o versioni successive verrà invece creata un'applicazione per Windows Phone Silverlight.

    ![][8]

2.  In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione, quindi scegliere **Gestisci pacchetti NuGet**.

    Verrà visualizzata la finestra di dialogo Gestisci pacchetti NuGet.

3.  Cercare `WindowsAzure.Messaging.Managed`, quindi fare clic su **Installa** e accettare le condizioni per l'uso.

    ![][9]

    Verrà scaricato, installato e aggiunto un riferimento alla libreria di Messaggistica di Azure per Windows tramite il [pacchetto NuGet WindowsAzure.Messaging.Managed][pacchetto NuGet WindowsAzure.Messaging.Managed].

4.  Aprire il file App.xaml.cs e aggiungere le istruzioni `using` seguenti:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5.  Nel codice seguente aggiungere quanto segue all'inizio del metodo **Application\_Launching** in App.xaml.cs:

        var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            await hub.RegisterNativeAsync(args.ChannelUri.ToString());
        });

    Assicurarsi di inserire il nome dell'hub e la stringa di connessione denominata **DefaultListenSharedAccessSignature** e ottenuta nella sezione precedente.
    Questo permette di recuperare il valore di ChannelURI per l'app dal Servizio di notifica Push di Microsoft e quindi di registrarlo con l'hub di notifica. Garantisce inoltre che il valore di ChannelURI venga registrato nell'hub di notifica a ogni avvio dell'applicazione.

    > [WACOM.NOTE]Questa esercitazione invia una notifica di tipo avviso popup al dispositivo. Quando si invia una notifica di tipo riquadro, è invece necessario chiamare il metodo **BindToShellTile** sul canale. Per supportare sia la notifica di tipo riquadro che quella di tipo avviso popup, chiamare sia **BindToShellTile** che **BindToShellToast**.

6.  In Esplora soluzioni espandere **Proprietà**, aprire il file WMAppManifest.xml, fare clic sulla scheda **Funzionalità** e assicurarsi che la funzionalità \*\*ID\_**CAP**\_PUSH\_NOTIFICATION\*\* sia selezionata.

    ![][10]

    In questo modo si garantirà che l'app sia in grado di ricevere notifiche push.

7.  Premere F5 per eseguire l'app.

    Verrà visualizzato un messaggio di registrazione.

## <a name="send"></a>Inviare notifiche dal back-end

È possibile inviare notifiche usando gli hub di notifica da qualsia back-end tramite l'[interfaccia REST][interfaccia REST]. In questa esercitazione vengono inviate notifiche con un'applicazione console .NET. Per un esempio relativo all'invio di notifiche da un back-end di Servizi mobili di Azure integrato con Hub di notifica, vedere **Introduzione alle notifiche push in Servizi mobili** ([Back-end .NET][Back-end .NET] | [Back-end JavaScript][Back-end .NET]). Per un esempio relativo all'invio di notifiche tramite le API REST, vedere **Come usare Hub di notifica da Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  Fare clic con il pulsante destro del mouse sulla soluzione, scegliere **Aggiungi** e **Nuovo progetto**, quindi in **Visual C#** fare clic su **Windows** e **Applicazione console** e infine su **OK**.

    ![][11]

    Una nuova applicazione console Visual C# verrà aggiunta alla soluzione. Questa operazione può essere eseguita anche in una soluzione separata.

2.  Fare clic su **Strumenti**, scegliere **Gestione pacchetti libreria**, quindi fare clic su **Console di Gestione pacchetti**.

    Verrà visualizzata la Console Gestione pacchetti.

3.  Nella finestra della console impostare **Progetto predefinito** sul nuovo progetto di applicazione console, quindi eseguire il comando seguente nella finestra della console:

        Install-Package WindowsAzure.ServiceBus

    Verrà aggiunto un riferimento ad Azure Service Bus SDK con il [pacchetto NuGet WindowsAzure.ServiceBus][pacchetto NuGet WindowsAzure.ServiceBus].

4.  Aprire il file Program.cs e aggiungere l'istruzione `using` seguente:

        using Microsoft.ServiceBus.Notifications;

5.  Nella classe **Program** aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

    Assicurarsi di sostituire il segnaposto relativo al nome dell'hub con il nome dell'hub di notifica visualizzato nella scheda **Hub di notifica del portale**: Sostituire anche il segnaposto relativo alla stringa di connessione con la stringa di connessione denominata **DefaultFullSharedAccessSignature** ottenuta nella sezione "Configurazione dell'hub di notifica".

    > [WACOM.NOTE]Assicurarsi di usare la stringa di connessione con accesso **Full**, non con accesso **Listen**. La stringa con accesso di tipo Listen non è autorizzata all'invio di notifiche.

6.  Aggiungere quindi la riga seguente nel metodo Main:

         SendNotificationAsync();
         Console.ReadLine();

7.  Con l'emulatore di Windows Phone in esecuzione e l'app chiusa, impostare il progetto di applicazione console come progetto di avvio predefinito, quindi premere F5 key per eseguire l'app.

    Verrà ricevuta una notifica di tipo avviso popup. Se si tocca il bannner dell'avviso popup, verrà caricata l'app.

Tutti i possibili payload sono disponibili negli argomenti relativi al [catalogo degli avvisi popup][catalogo degli avvisi popup] e al [catalogo dei riquadri][catalogo dei riquadri] su MSDN.

## <a name="next-steps"> </a>Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi Windows Phone 8. Per rivolgersi a utenti specifici, fare riferimento all'esercitazione [Utilizzo di Hub di notifica per inviare notifiche push agli utenti][Utilizzo di Hub di notifica per inviare notifiche push agli utenti]. Se si desidera segmentare gli utenti per gruppi di interesse, consultare [Utilizzo di Hub di notifica per inviare le ultime notizie][Utilizzo di Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'utilizzo di Hub di notifica, vedere la pagina delle [linee guida su Hub di notifica][linee guida su Hub di notifica].



  [Windows Universal]: /it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /it-it/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /it-it/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /it-it/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /it-it/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [1]: /it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  [invio di notifiche agli utenti]: /it-it/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/
  [API REST degli hub di notifica]: http://msdn.microsoft.com/it-it/library/dn223264.aspx
  [Creare l'hub di notifica]: #configure-hub
  [Connessione dell'app all'hub di notifica]: #connecting-app
  [Inviare notifiche dal back-end]: #send
  [Visual Studio 2012 Express per Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  []: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
  [2]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
  [3]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
  [4]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
  [5]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png
  [6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
  [modalità con autenticazione del Servizio di notifica Push di Microsoft]: http://msdn.microsoft.com/it-it/library/windowsphone/develop/ff941099(v=vs.105).aspx
  [7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
  [8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
  [9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
  [pacchetto NuGet WindowsAzure.Messaging.Managed]: http://nuget.org/packages/WindowsAzure.Messaging.Managed/
  [10]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
  [interfaccia REST]: http://msdn.microsoft.com/it-it/library/windowsazure/dn223264.aspx
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/
  [Java]: /it-it/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /it-it/documentation/articles/notification-hubs-php-backend-how-to/
  [11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
  [pacchetto NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [catalogo degli avvisi popup]: http://msdn.microsoft.com/it-it/library/windowsphone/develop/jj662938(v=vs.105).aspx
  [catalogo dei riquadri]: http://msdn.microsoft.com/it-it/library/windowsphone/develop/hh202948(v=vs.105).aspx
  [Utilizzo di Hub di notifica per inviare notifiche push agli utenti]: /it-it/manage/services/notification-hubs/notify-users-aspnet
  [Utilizzo di Hub di notifica per inviare le ultime notizie]: /it-it/manage/services/notification-hubs/breaking-news-dotnet
  [linee guida su Hub di notifica]: http://msdn.microsoft.com/it-it/library/jj927170.aspx
