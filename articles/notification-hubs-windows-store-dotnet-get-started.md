<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Get started with notification hubs" pageTitle="Get started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Introduzione ad Hub di notifica

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="App Windows universali" class="current">App Windows universali</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/it-it/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/it-it/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Questo argomento illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione di Windows Store o per Windows Phone 8.1 (non Silverlight). Se le notifiche sono destinate ad app per Windows Phone 8.1 Silverlight, fare riferimento alla versione [Windows Phone][1].
In questa esercitazione si crea un'app di Windows Store vuota che riceve notifiche push tramite Servizi notifica Push Windows (WNS). Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app per le notifiche push][Registrazione dell'app per le notifiche push]
2.  [Configurazione dell'hub di notifica][Configurazione dell'hub di notifica]
3.  [Connessione dell'app all'hub di notifica][Connessione dell'app all'hub di notifica]
4.  [Invio di notifiche dal back-end][Invio di notifiche dal back-end]

In questa esercitazione viene illustrato uno scenario di trasmissione semplice tramite Hub di notifica. Seguire anche l'esercitazione successiva per imparare a utilizzare gli hub di notifica destinati a gruppi di dispositivi e a utenti specifici. Per completare questa esercitazione, è necessario disporre di:

-   Microsoft Visual Studio Express 2013 per Windows con Update 2
    Per creare un progetto di app universale è necessaria questa versione di Visual Studio. Se si desidera solo creare un'app di Windows Store, è necessario Visual Studio Express 2012 per Windows 8.

-   Account di Windows Store attivo

-   Un account Azure attivo.
    Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub notifica relative ad app di Windows Store.

## <a name="register"></a>Registrazione dell'app di Windows Store

Per inviare notifiche push ad app di Windows Store da Servizi mobili, è necessario inviare l'app a Windows Store e quindi configurare l'hub di notifica per l'integrazione con Servizi notifica Push Windows.

1.  Se l'app non è ancora stata registrata, passare alla pagina di [invio di app][invio di app] nel Centro per sviluppatori di app di Windows Store, accedere con il proprio account Microsoft e quindi fare clic su **Nome app**.

    ![][0]

2.  Immettere un nome per l'app in **Nome app**, fare clic su **Riserva nome applicazione** e quindi su **Salva**.

    ![][2]

    Verrà creata una nuova registrazione a Windows Store per l'app.

3.  In Visual Studio creare un nuovo progetto di app di Windows Store in Visual C# usando il modello **Applicazione vuota**.

    ![][3]

4.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di app di Windows Store, scegliere **Store**, quindi fare clic su **Associa applicazione a Store**.

    ![][4]

    Viene visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.

5.  Nella procedura guidata fare clic su **Accedi** e quindi accedere con il proprio account Microsoft.

6.  Fare clic sull'app registrata nel passaggio 2, fare clic su **Avanti** e quindi su **Associa**.

    ![][5]

    Le necessarie informazioni di registrazione a Windows Store verranno aggiunte al manifesto dell'applicazione.

7.  (Facoltativo) Ripetere i passaggi da 4 a 6 per il progetto di app di Windows Phone Store.

8.  Nella pagina di Windows Dev Center per la nuova app fare clic su **Servizi**.

    ![][6]

9.  Nella pagina **Servizi** fare clic su **sito di servizi Live** nel paragrafo **Servizi mobili di Microsoft Azure**.

    ![][7]

10. Nella scheda **Impostazioni app** prendere nota dei valori specificati nei campi **Chiave privata client** e **Identificatore di sicurezza del pacchetto (SID)**.

    ![][8]

    <div class="dev-callout"><b>Nota sulla sicurezza</b>
<p>Il segreto client e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri utenti n&eacute; distribuirli con l'app.</p>
</div>

## <a name="configure-hub"></a>Configurazione dell'hub di notifica

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure] e fare clic su **NEW** nella parte inferiore della schermata.

2.  Fare clic su **App Services**, selezionare **Service Bus**, **Notification Hub**, quindi **Quick Create**.

    ![][9]

3.  Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Create a new Notification Hub**.

    ![][10]

4.  Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic sulla scheda **Configure** nella parte superiore.

    ![][11]

5.  Fare clic sulla scheda **Notification Hubs** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

    ![][12]

6.  Fare clic sulla scheda **Configura** nella parte superiore, immettere i valori di **Segreto client** e **Package SID** ottenuti da Servizi notifica Push Windows nella sezione precedente, quindi fare clic su **Salva**.

    ![][13]

7.  Selezionare la scheda **Dashboard** nella parte superiore e quindi fare clic su **Connection Information**. Prendere nota delle due stringhe di connessione.

    ![][14]

L'hub di notifica è ora configurato per l'uso con Servizi notifica Push Windows e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche.

## <a name="connecting-app"></a>Connessione dell'app all'hub di notifica

1.  In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione, quindi scegliere **Gestisci pacchetti NuGet**.

    Viene visualizzata la finestra di dialogo Gestisci pacchetti NuGet.

2.  Cercare `WindowsAzure.Messaging.Managed` e fare clic su **Installa**, selezionare tutti i progetti inclusi nella soluzione e accettare le condizioni per l'utilizzo.

    ![][15]

    In tutti i progetti viene scaricato, installato e aggiunto un riferimento alla libreria di messaggistica di Azure usando il [pacchetto NuGet WindowsAzure.Messaging.Managed][pacchetto NuGet WindowsAzure.Messaging.Managed].

3.  Aprire il file di progetto App.xaml.cs e aggiungere le istruzioni `using` seguenti:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

    In un progetto universale questo file si trova nella cartella `<project_name>.Shared`.

4.  In App.xaml.cs aggiungere inoltre alla classe **App** la definizione del metodo **InitNotificationsAsync** seguente:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }

        }

    Questo codice consente di recuperare il valore di ChannelURI per l'app da Servizi notifica Push Windows e quindi di registrarlo con l'hub di notifica.

    > [WACOM.NOTE]Assicurarsi di sostituire il segnaposto relativo al nome dell'hub con il nome dell'hub di notifica visualizzato nella scheda **Hub di notifica** del portale (**mynotificationhub2** nell'esempio precedente). Sostituire inoltre il segnaposto relativo alla stringa di connessione con la stringa di connessione **DefaultListenSharedAccessSignature** ottenuta nella sezione precedente.

5.  All'inizio del gestore dell'evento **OnLaunched** in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo **InitNotificationsAsync**:

        InitNotificationsAsync();

    In questo modo si garantisce che il valore di ChannelURI venga registrato nell'hub di notifica a ogni avvio dell'applicazione.

6.  In Esplora soluzioni fare doppio clic sul file **Package.appxmanifest** dell'app di Windows Store e in **Notifiche** impostare **Popup supportati** su **Sì**:

    ![][16]

    Scegliere **Salva tutto** dal menu **File**.

7.  (Facoltativo) Ripetere il passaggio precedente nel progetto di app di Windows Phone Store.

8.  Premere **F5** per eseguire l'app. Verrà visualizzata una finestra di dialogo popup con la chiave di registrazione.

    ![][17]

9.  (Facoltativo) Ripetere il passaggio precedente per eseguire l'altro progetto.

L'app è ora pronta per ricevere notifiche di tipo avviso popup.

## <a name="send"></a>Invio di una notifica dal back-end

È possibile usare Hub di notifica per inviare notifiche da qualsiasi back-end tramite l'[interfaccia REST][interfaccia REST]. In questa esercitazione si inviano notifiche con un'applicazione console .NET. Per un esempio di invio di notifiche da un back-end di Servizi mobili di Azure con Hub di notifica, vedere **Introduzione alle notifiche push in Servizi mobili** ([Back-end .NET][Back-end .NET] | [Back-end JavaScript][Back-end .NET]). Per un esempio di invio di notifiche con le API REST, vedere **Come usare Hub di notifica da Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  Fare clic con il pulsante destro del mouse sulla soluzione, scegliere **Aggiungi** e **Nuovo progetto**, quindi in **Visual C#** fare clic su **Windows** e **Applicazione console** e infine fare clic su **OK**.

    ![][18]

    Alla soluzione viene aggiunta una nuova applicazione console in Visual C#: È possibile eseguire questa operazione anche in una soluzione separata.

2.  Fare clic con il pulsante destro del mouse sulla soluzione, scegliere **Strumenti**, quindi fare clic su **Gestione pacchetti libreria** e infine su **Console di Gestione Pacchetti**.

    Viene visualizzata la console di Gestione pacchetti.

3.  Nella finestra della console impostare il nuovo progetto di applicazione console come progetto predefinito, quindi eseguire il comando seguente:

        Install-Package WindowsAzure.ServiceBus

    Viene aggiunto un riferimento ad Azure Service Bus SDK con il [pacchetto NuGet WindowsAzure.ServiceBus][pacchetto NuGet WindowsAzure.ServiceBus].

4.  Aprire il file Program.cs e aggiungere l'istruzione `using` seguente:

        using Microsoft.ServiceBus.Notifications;

5.  Nella classe **Program** aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

    Assicurarsi di sostituire il segnaposto relativo al nome dell'hub con il nome dell'hub di notifica visualizzato nella scheda **Hub di notifica** del portale. Sostituire inoltre il segnaposto relativo alla stringa di connessione con la stringa di connessione denominata **DefaultFullSharedAccessSignature** ottenuta nella sezione Configurazione dell'hub di notifica.

    > [WACOM.NOTE]Assicurarsi di usare la stringa di connessione con accesso di tipo **Completo**, non con accesso di tipo **Attesa**. La stringa con accesso di tipo Attesa non dispone infatti delle autorizzazioni per l'invio di notifiche.

6.  Aggiungere quindi le righe seguenti nel metodo **Main**:

         SendNotificationAsync();
         Console.ReadLine();

7.  Con l'applicazione console impostata come progetto di avvio, premere **F5** per eseguire l'applicazione.

    ![][19]

    Tutti i dispositivi registrati riceveranno una notifica di tipo avviso popup. Facendo clic o toccando il banner dell'avviso popup l'app viene caricata.

Per trovare tutti i payload supportati, è possibile consultare gli argomenti relativi al [catalogo degli avvisi popup][catalogo degli avvisi popup], al [catalogo dei riquadri][catalogo dei riquadri] e alla [panoramica delle notifiche][panoramica delle notifiche] su MSDN.

## <a name="next-steps"> </a>Passaggi successivi

In questo semplice esempio le notifiche di trasmissione sono state inviate a tutti i dispositivi Windows. Per rivolgersi a utenti specifici, fare riferimento all'esercitazione [Utilizzo di Hub di notifica per inviare notifiche push agli utenti][Utilizzo di Hub di notifica per inviare notifiche push agli utenti]. Se si desidera segmentare gli utenti per gruppi di interesse, vedere [Utilizzo di Hub di notifica per inviare le ultime notizie][Utilizzo di Hub di notifica per inviare le ultime notizie]. Per ulteriori informazioni sull'uso di Hub di notifica, vedere la pagina delle [linee guida su Hub di notifica][linee guida su Hub di notifica].



  [Windows Phone]: /it-it/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [1]: /it-it/documentation/articles/notification-hubs-windows-phone-get-started/
  [Registrazione dell'app per le notifiche push]: #register
  [Configurazione dell'hub di notifica]: #configure-hub
  [Connessione dell'app all'hub di notifica]: #connecting-app
  [Invio di notifiche dal back-end]: #send
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [invio di app]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  [0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
  [2]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
  [3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
  [4]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
  [5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
  [6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
  [7]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
  [8]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
  [portale di gestione di Azure]: https://manage.windowsazure.com/
  [9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
  [10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
  [11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
  [12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
  [13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
  [14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
  [15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png
  [pacchetto NuGet WindowsAzure.Messaging.Managed]: http://nuget.org/packages/WindowsAzure.Messaging.Managed/
  [16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
  [17]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
  [interfaccia REST]: http://msdn.microsoft.com/it-it/library/windowsazure/dn223264.aspx
  [Back-end .NET]: /it-it/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Java]: /it-it/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /it-it/documentation/articles/notification-hubs-php-backend-how-to/
  [18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
  [pacchetto NuGet WindowsAzure.ServiceBus]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
  [catalogo degli avvisi popup]: http://msdn.microsoft.com/it-it/library/windows/apps/hh761494.aspx
  [catalogo dei riquadri]: http://msdn.microsoft.com/it-it/library/windows/apps/hh761491.aspx
  [panoramica delle notifiche]: http://msdn.microsoft.com/it-it/library/windows/apps/hh779719.aspx
  [Utilizzo di Hub di notifica per inviare notifiche push agli utenti]: /it-it/manage/services/notification-hubs/notify-users-aspnet
  [Utilizzo di Hub di notifica per inviare le ultime notizie]: /it-it/manage/services/notification-hubs/breaking-news-dotnet
  [linee guida su Hub di notifica]: http://msdn.microsoft.com/it-it/library/jj927170.aspx
