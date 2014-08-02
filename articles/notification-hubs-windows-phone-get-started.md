<properties linkid="develop-notificationhubs-tutorials-get-started-windowsphone" urlDisplayName="Get Started" pageTitle="Get Started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

Introduzione ad Hub di notifica
===============================

[Windows Store C#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/en-us/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/en-us/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/en-us/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/en-us/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/en-us/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/en-us/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

In questo argomento viene illustrato come utilizzare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Windows Phone 8. In questa esercitazione si creerà un'app per Windows Phone 8 vuota che riceve notifiche push tramite il Servizio di notifica Push di Microsoft. Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

In questa esercitazione vengono descritte le operazioni seguenti per abilitare le notifiche push:

1.  [Creazione dell'hub di notifica](#configure-hub)
2.  [Connessione dell'app all'hub di notifica](#connecting-app)
3.  [Invio di notifiche dal back-end](#send)

In questa esercitazione viene illustrato uno scenario di trasmissione semplice tramite hub di notifica. Seguire anche l'esercitazione successiva per imparare a utilizzare gli hub di notifica destinati a gruppi di dispositivi e a utenti specifici. Per completare questa esercitazione, è necessario disporre di:

-   [Visual Studio 2012 Express per Windows Phone](https://go.microsoft.com/fwLink/p/?LinkID=268374) o versione successiva

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub notifica relative ad app per Windows Phone 8.

**Nota**

Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/en-us/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

Creazione dell'hub di notificaCreazione dell'hub di notifica
------------------------------------------------------------

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/) e fare clic su **+NEW** nella parte inferiore della schermata.

2.  Fare clic su **Servizi app**, selezionare **Bus di servizio**, **Hub di notifica**, quindi **Creazione rapida**.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png)

3.  Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Create a new Notification Hub**.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png)

4.  Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic sulla scheda **Configure** nella parte superiore.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png)

5.  Fare clic sulla scheda **Notification Hubs** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png)

6.  Fare clic su **Connection Information** nella parte inferiore. Prendere nota delle due stringhe di connessione.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png)

7.  Fare clic sulla scheda **Configure**, quindi selezionare la casella di controllo **Enable unauthenticated push notifications** nella sezione **Windows Phone notifications settings**.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png)

A questo punto si dispone delle stringhe di connessione necessarie per registrare l'app per Windows Phone 8 e inviare le notifiche.

**Nota**

In questa esercitazione verrà utilizzato il Servizio di notifica Push di Microsoft in modalità senza autenticazione. Tale modalità prevede restrizioni in relazione alle notifiche che è possibile inviare a ciascun canale. In Hub di notifica è supportata la [modalità con autenticazione del Servizio di notifica Push di Microsoft](http://msdn.microsoft.com/en-us/library/windowsphone/develop/ff941099(v=vs.105).aspx).

Connessione dell'appConnessione dell'app all'hub di notifica
------------------------------------------------------------

1.  In Visual Studio creare una nuova applicazione per Windows Phone 8.

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png)

2.  Aggiungere un riferimento alla libreria di Messaggistica di Azure per Windows Store utilizzando il [pacchetto NuGet WindowsAzure.Messaging.Managed](http://nuget.org/packages/WindowsAzure.Messaging.Managed/). Nel menu di Visual Studio fare clic su **Strumenti**, quindi scegliere **Gestione pacchetti libreria** e infine **Console di Gestione pacchetti**. Quindi nella finestra della console digitare:

		Install-Package WindowsAzure.Messaging.Managed

    e premere INVIO.

3.  Aprire il file App.xaml.cs e aggiungere le istruzioni `using` seguenti:

		using Microsoft.Phone.Notification;
		using Microsoft.WindowsAzure.Messaging;

4.  Nel codice seguente aggiungere quanto segue all'inizio del metodo **Application_Launching** in App.xaml.cs:

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

    Assicurarsi di inserire il nome dell'hub e la stringa di connessione denominata **DefaultListenSharedAccessSignature** e ottenuta nella sezione precedente. Questo codice consente di recuperare il valore di ChannelURI per l'app dal Servizio di notifica Push di Microsoft e quindi di registrarlo con l'hub di notifica. Garantisce inoltre che il valore di ChannelURI venga registrato nell'hub di notifica a ogni avvio dell'applicazione.

    **Nota**

    In questa esercitazione viene inviata una notifica di tipo avviso popup al dispositivo. Quando si invia una notifica di tipo riquadro, è invece necessario chiamare il metodo **BindToShellTile** sul canale. Per supportare sia la notifica di tipo riquadro che quella di tipo avviso popup, chiamare sia **BindToShellTile** che **BindToShellToast**.

5.  In Esplora soluzioni espandere **Proprietà**, aprire il file WMAppManifest.xml, fare clic sulla scheda **Funzionalità** e assicurarsi che la funzionalità **ID__CAP__PUSH_NOTIFICATION** sia selezionata.

   	![](./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png)

   	In questo modo si garantirà che l'app sia in grado di ricevere notifiche push.

6.  Premere F5 per eseguire l'app.

Invio di notificheInvio di notifiche dal back-end
-------------------------------------------------

È possibile utilizzare Hub di notifica per inviare notifiche da qualsiasi back-end tramite l'[interfaccia REST](http://msdn.microsoft.com/en-us/library/windowsazure/dn223264.aspx). In questa esercitazione vengono inviate notifiche con un'app console .NET e con un servizio mobile tramite uno script node.

Per inviare notifiche tramite un'app .NET:

1.  Creare una nuova applicazione console in Visual C#:

   	![](./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png)

2.  Aggiungere un riferimento ad Azure Service Bus SDK con il [pacchetto NuGet WindowsAzure.ServiceBus](http://nuget.org/packages/WindowsAzure.ServiceBus/). Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi scegliere **Gestione pacchetti libreria** e infine **Console di Gestione pacchetti**. Quindi nella finestra della console digitare:

		Install-Package WindowsAzure.ServiceBus

    e premere INVIO.

3.  Aprire il file Program.cs e aggiungere l'istruzione `using` seguente:

		using Microsoft.ServiceBus.Notifications;

4.  Nella classe `Program` aggiungere il metodo seguente:

		private static async void SendNotificationAsync()
		{
			NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
			string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
				"<wp:Notification xmlns:wp=\"WPNotification\">" +
					"<wp:Toast>" +
						"<wp:Text1>Hello from a .NET App!</wp:Text1>" +
					"</wp:Toast> " +
				"</wp:Notification>";
			await hub.SendMpnsNativeNotificationAsync(toast);
		}

    Assicurarsi di inserire il nome dell'hub e la stringa di connessione denominata DefaultFullSharedAccessSignature e ottenuta nella sezione "Configurazione dell'hub di notifica". Si noti che questa è la stringa di connessione con accesso Full, non con accesso Listen.

5.  Aggiungere quindi la riga seguente nel metodo Main:

		SendNotificationAsync();
		Console.ReadLine();

6.  Premere F5 per eseguire l'app. Si dovrebbe ricevere una notifica di tipo avviso popup. Verificare che l'emulatore di Windows Phone sia in esecuzione e che l'app sia chiusa.

Tutti i possibili payload sono disponibili nel [catalogo degli avvisi popup](http://msdn.microsoft.com/en-us/library/windowsphone/develop/jj662938(v=vs.105).aspx) e nel [catalogo dei riquadri](http://msdn.microsoft.com/en-us/library/windowsphone/develop/hh202948(v=vs.105).aspx) su MSDN.

Passaggi successivi
-------------------

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi Windows Phone 8. Per rivolgersi a utenti specifici, fare riferimento all'esercitazione [Utilizzo di Hub di notifica per inviare notifiche push agli utenti](/en-us/manage/services/notification-hubs/notify-users-aspnet). Se si desidera segmentare gli utenti per gruppi di interesse, consultare [Utilizzo di Hub di notifica per inviare le ultime notizie](/en-us/manage/services/notification-hubs/breaking-news-dotnet). Per ulteriori informazioni sull'utilizzo di Hub di notifica, vedere la pagina delle [linee guida su Hub di notifica](http://msdn.microsoft.com/en-us/library/jj927170.aspx).

