<properties 
	pageTitle="Introduzione ad Hub di notifica di Azure" 
	description="Informazioni su come usare Hub di notifica di Azure per inviare notifiche push." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor="dwrede"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="09/24/2014" 
	ms.author="glenga"/>
# Introduzione ad Hub di notifica

<div class="dev-center-tutorial-selector sublanding"><a href="/it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/it-it/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/it-it/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/it-it/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/it-it/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/it-it/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

Questo argomento illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Windows Phone 8 o Windows Phone 8.1 Silverlight. Se si usa Windows Phone 8.1 (non Silverlight), fare riferimento alla versione [Windows Universal](/it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/).
In questa esercitazione si creerà un'app di Windows Phone 8 vuota che riceve notifiche push tramite il Servizio di notifica Push di Microsoft. Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

> [AZURE.NOTE] Hub di notifica per Windows Phone SDK non supporta l'uso di WNS con app di Windows Phone 8.1 Silverlight. Per usare WNS (invece di MPNS) con app di Windows Phone 8.1 Silverlight, è necessario configurare le credenziali WNS, come illustrato in [Introduzione a Windows Universal](/it-it/documentation/articles/notification-hubs-windows-store-dotnet-get-started/). Sarà quindi possibile effettuare la registrazione dal back-end, come illustrato nell'esercitazione sull'[invio di notifiche agli utenti](/it-it/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/), oppure usare le [API REST degli hub di notifica](http://msdn.microsoft.com/library/dn223264.aspx).

In questa esercitazione vengono descritte le seguenti operazioni per abilitare le notifiche push:

1. [Creare l'hub di notifica]
2. [Connettere l'app all'hub di notifica]
3. [Inviare notifiche dal back-end]

Questa esercitazione illustra uno scenario di trasmissione semplice tramite hub di notifica. Seguire anche l'esercitazione successiva per imparare a usare gli hub di notifica destinati a gruppi di dispositivi e a utenti specifici. Per completare questa esercitazione, è necessario soddisfare i seguenti requisiti:

+ [Visual Studio 2012 Express per Windows Phone] o versione successiva.

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni sugli hub di notifica relative ad app di Windows Phone 8. 

> [AZURE.NOTE] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F%20 target="_blank").

##<a name="configure-hub"></a>Creare l'hub di notifica

1. Accedere al [portale di gestione di Azure] e fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, selezionare **Bus di servizio**, **Hub di notifica**, quindi **Creazione rapida**.

   	![][7]

3. Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Crea un nuovo hub di notifica**.

   	![][8]

4. Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic su **Configura** nella parte superiore.

   	![][9]

5. Fare clic sulla scheda **Hub di notifica** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

   	![][10]

6. Fare clic su **Informazioni di connessione** nella parte inferiore. Prendere nota delle due stringhe di connessione.

   	![][12]

7. Fare clic sulla scheda **Configura**, quindi selezionare la casella di controllo **Abilita notifiche push non autenticate** nella sezione relativa alle **impostazioni di notifica di Windows Phone**.

   	![][15]

A questo punto si dispone delle stringhe di connessione necessarie per registrare l'app di Windows Phone 8 e inviare le notifiche.

> [AZURE.NOTE] In questa esercitazione verrà usato il Servizio di notifica Push di Microsoft in modalità senza autenticazione. Tale modalità prevede restrizioni in relazione alle notifiche che è possibile inviare a ciascun canale. Hub di notifica supporta la [modalità con autenticazione del Servizio di notifica Push di Microsoft](http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx). <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.-->

##<a name="connecting-app"></a>Connettere l'app all'hub di notifica

1. In Visual Studio creare una nuova applicazione per Windows Phone 8.

   	![][13]

	In Visual Studio 2013 Update 2 o versioni successive verrà invece creata un'applicazione per Windows Phone Silverlight.
	
	![][11]	

2. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione, quindi scegliere **Gestisci pacchetti NuGet**. 

	Verrà visualizzata la finestra di dialogo Gestisci pacchetti NuGet.

3. Cercare  `WindowsAzure.Messaging.Managed`, quindi fare clic su **Installa** e accettare le condizioni per l'uso. 

	![][20]

	Verrà scaricato, installato e aggiunto un riferimento alla libreria di Messaggistica di Azure per Windows tramite il pacchetto <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet</a>. 

4. Aprire il file App.xaml.cs e aggiungere le seguenti istruzioni  `using`:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Nel seguente codice aggiungere quanto segue all'inizio del metodo **Application_Launching** in App.xaml.cs:
	
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
    Questo codice consente di recuperare il valore di ChannelURI per l'app dal Servizio di notifica Push di Microsoft e quindi di registrarlo con l'hub di notifica. Garantisce inoltre che il valore di ChannelURI venga registrato nell'hub di notifica a ogni avvio dell'applicazione.

	>[AZURE.NOTE]In questa esercitazione viene inviata una notifica di tipo avviso popup al dispositivo. Quando si invia una notifica di tipo riquadro, è invece necessario chiamare il metodo **BindToShellTile** sul canale. Per supportare sia la notifica di tipo riquadro che quella di tipo avviso popup, chiamare sia **BindToShellTile** che **BindToShellToast**. 
    
6. In Esplora soluzioni espandere **Proprietà**, aprire il file WMAppManifest.xml, fare clic sulla scheda **Funzionalità** e assicurarsi che la funzionalità **ID___CAP___PUSH_NOTIFICATION** sia selezionata.

   	![][14]

   	In questo modo si garantirà che l'app sia in grado di ricevere notifiche push.
	
7. Premere F5 per eseguire l'app.

	Verrà visualizzato un messaggio di registrazione.

##<a name="send"></a>Inviare notifiche dal back-end

È possibile inviare notifiche usando gli hub di notifica da qualsiasi back-end tramite l'<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaccia REST</a>. In questa esercitazione vengono inviate notifiche con un'applicazione console .NET. Per un esempio relativo all'invio di notifiche da un back-end di Servizi mobili di Azure integrato con Hub di notifica, vedere **Introduzione alle notifiche push in Servizi mobili** (back-end [.NET ](/it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/) | [back-end JavaScript](/it-it/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/)).  Per un esempio relativo all'invio di notifiche tramite le API REST, vedere **Come usare Hub di notifica da Java/PHP** ([Java](/it-it/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/it-it/documentation/articles/notification-hubs-php-backend-how-to/)).

1. Fare clic con il pulsante destro del mouse sulla soluzione, scegliere **Aggiungi** e **Nuovo progetto**, quindi in **Visual C#** fare clic su **Windows** e **Applicazione console**, infine su **OK**. 

   	![][6]

	Una nuova applicazione console Visual C# verrà aggiunta alla soluzione. Questa operazione può essere eseguita anche in una soluzione separata. 

4. Fare clic su **Strumenti**, **Gestione pacchetti di librerie** e infine su **Console di Gestione pacchetti**. 

	Verrà visualizzata la Console Gestione pacchetti.

6. Nella finestra della console impostare **Progetto predefinito** sul nuovo progetto di applicazione console, quindi eseguire il seguente comando nella finestra della console:

        Install-Package WindowsAzure.ServiceBus
    
	Verrà aggiunto un riferimento ad Azure Service Bus SDK con il <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacchetto WindowsAzure.ServiceBus NuGet</a>. 

5. Aprire il file Program.cs e aggiungere la seguente istruzione `using`:

        using Microsoft.ServiceBus.Notifications;

6. Nella classe **Program** aggiungere il seguente metodo:

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

	Assicurarsi di sostituire il segnaposto relativo al nome dell'hub con il nome dell'hub di notifica visualizzato nella scheda **Hub di notifica** del portale. Sostituire anche il segnaposto relativo alla stringa di connessione con la stringa di connessione denominata **DefaultFullSharedAccessSignature** ottenuta nella sezione "Configurare l'hub di notifica". 

	>[AZURE.NOTE]Assicurarsi di usare la stringa di connessione con accesso **Full**, non con accesso **Listen**. La stringa con accesso di tipo Listen non è autorizzata all'invio di notifiche.

4. Aggiungere quindi la seguente riga nel metodo Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Con l'emulatore di Windows Phone in esecuzione e l'app chiusa, impostare il progetto di applicazione console come progetto di avvio predefinito, quindi premere il tasto F5 per eseguire l'app. 

	Verrà ricevuta una notifica di tipo avviso popup. Se si tocca il banner dell'avviso popup, verrà caricata l'app.

Tutti i possibili payload sono disponibili negli argomenti relativi al [catalogo degli avvisi popup] e al [catalogo dei riquadri] su MSDN.

## <a name="next-steps"> </a>Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi Windows Phone 8. Per rivolgersi a utenti specifici, fare riferimento all'esercitazione [Uso di Hub di notifica per inviare notifiche push agli utenti]. Se si desidera segmentare gli utenti per gruppi di interesse, consultare [Uso di Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'uso di Hub di notifica, vedere le [informazioni aggiuntive su Hub di notifica].

<!-- Anchors. -->
[Creare l'hub di notifica]: #configure-hub
[Connettere l'app all'hub di notifica]: #connecting-app
[Inviare notifiche dal back-end]: #send
[Passaggi successivi]:#next-steps

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express per Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Introduzione a Servizi mobili]: /it-it/develop/mobile/tutorials/get-started/#create-new-service

[Portale di gestione di Azure]: https://manage.windowsazure.com/
[Informazioni aggiuntive su Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Phone 8]: TBD!!!
[Modalità di autenticazione MPNS]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Uso di Hub di notifica per inviare notifiche push agli utenti]: /it-it/manage/services/notification-hubs/notify-users-aspnet
[Uso di Hub di notifica per inviare le ultime notizie]: /it-it/manage/services/notification-hubs/breaking-news-dotnet
[catalogo degli avvisi popup]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catalogo dei riquadri]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx

<!--HONumber=45--> 
