<properties 
	pageTitle="Introduzione ad Hub di notifica di Azure" 
	description="Informazioni su come usare Hub di notifica di Azure per inviare notifiche push." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="dwrede"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>
# Introduzione ad Hub di notifica

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##Panoramica

Questo argomento descrive come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Windows Phone 8 o Windows Phone 8.1 Silverlight. Se si usa Windows Phone 8.1 (non Silverlight), fare riferimento alla versione [Windows Universal](notification-hubs-windows-store-dotnet-get-started.md). In questa esercitazione si creerà un'app per Windows Phone 8 vuota che riceve notifiche push tramite il Servizio di notifica Push di Microsoft. Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

> [AZURE.NOTE] Hub di notifica per Windows Phone SDK non supporta l'uso di WNS con app di Windows Phone 8.1 Silverlight. Per usare WNS (invece di MPNS) con le app di Windows Phone 8.1 Silverlight, completare la seguente [Esercitazione su Hub di notifica - WP Silverlight] di esempio che usa API REST

In questa esercitazione viene illustrato uno scenario di trasmissione semplice tramite hub di notifica.

##Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

+ [Visual Studio 2012 Express per Windows Phone] o versione successiva

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub notifica relative ad app per Windows Phone 8.

> [AZURE.NOTE] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

##Creare l'hub di notifica

1. Accedere al [portale di gestione di Azure] e fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, selezionare **Bus di servizio**, **Hub di notifica**, quindi **Creazione rapida**.

   	![][7]

3. Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Create a new Notification Hub**.

   	![][8]

4. Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic sulla scheda **Configure** nella parte superiore.

   	![][9]

5. Fare clic sulla scheda **Notification Hubs** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

   	![][10]

6. Fare clic su **Connection Information** nella parte inferiore. Prendere nota delle due stringhe di connessione.

   	![][12]

7. Fare clic sulla scheda **Configure**, quindi selezionare la casella di controllo **Enable unauthenticated push notifications** nella sezione **Windows Phone notifications settings**.

   	![][15]

A questo punto si dispone delle stringhe di connessione necessarie per registrare l'app di Windows Phone 8 e inviare le notifiche.

> [AZURE.NOTE]In questa esercitazione verrà usato il Servizio di notifica Push di Microsoft in modalità senza autenticazione. Tale modalità prevede restrizioni in relazione alle notifiche che è possibile inviare a ciascun canale. In Hub di notifica è supportata la [modalità con autenticazione del Servizio di notifica Push di Microsoft](http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx).
<!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.-->

##Connettere l'app all'hub di notifica

1. In Visual Studio creare una nuova applicazione per Windows Phone 8.

   	![][13]

	In Visual Studio 2013 Update 2 o versioni successive verrà invece creata un'applicazione per Windows Phone Silverlight.
	
	![][11]

2. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione, quindi scegliere **Gestisci pacchetti NuGet**. 

	Verrà visualizzata la finestra di dialogo Gestisci pacchetti NuGet.

3. Cercare `WindowsAzure.Messaging.Managed`, quindi fare clic su **Installa** e accettare le condizioni per l'utilizzo.

	![][20]

	Verrà scaricato, installato e aggiunto un riferimento alla libreria di Messaggistica di Azure per Windows tramite il <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">pacchetto WindowsAzure.Messaging.Managed NuGet</a>.

4. Aprire il file App.xaml.cs e aggiungere le istruzioni `using` seguenti:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Nel codice seguente aggiungere quanto segue all'inizio del metodo **Application_Launching** in App.xaml.cs:
	
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
    
6. In Esplora soluzioni espandere **Proprietà**, aprire il file WMAppManifest.xml, fare clic sulla scheda **Funzionalità** e assicurarsi che la funzionalità **ID__CAP__PUSH_NOTIFICATION** sia selezionata.

   	![][14]

   	In questo modo si garantirà che l'app sia in grado di ricevere notifiche push.
	
7. Premere F5 per eseguire l'app.

	Verrà visualizzato un messaggio di registrazione.

##Inviare notifiche dal back-end

È possibile inviare notifiche usando Hub di notifica da qualsiasi back-end tramite l'<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaccia REST</a>. In questa esercitazione vengono inviate notifiche con un'applicazione console .NET. Per un esempio di invio di notifiche da un back-end di Servizi mobili di Azure con Hub di notifica, vedere **Introduzione alle notifiche push in Servizi mobili** ([Back-end .NET](../mobile-services-javascript-backend-windows-phone-get-started-push.md) | [Back-end JavaScript](../mobile-services-javascript-backend-windows-phone-get-started-push.md)). Per un esempio di invio di notifiche con le API REST, vedere **Come usare Hub di notifica da Java/PHP** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. Fare clic con il pulsante destro del mouse sulla soluzione, scegliere **Aggiungi** e **Nuovo progetto**, quindi in **Visual C#** fare clic su **Windows** e **Applicazione console** e infine fare clic su **OK**. 

   	![][6]

	Una nuova applicazione console Visual C# viene aggiunta alla soluzione. Questa operazione può essere eseguita anche in una soluzione separata. 

4. Fare clic con il pulsante destro del mouse sulla soluzione, scegliere **Strumenti**, quindi fare clic su **Gestione pacchetti libreria** e infine su **Console di Gestione Pacchetti**. 

	Verrà visualizzata la console di Gestione pacchetti.

6. Nella finestra della console impostare il nuovo progetto di applicazione console come progetto predefinito****, quindi eseguire il comando seguente:

        Install-Package WindowsAzure.ServiceBus
    
	Viene aggiunto un riferimento ad Azure Service Bus SDK con il <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacchetto NuGet WindowsAzure.ServiceBus</a>.

5. Aprire il file Program.cs e aggiungere l'istruzione `using` seguente:

        using Microsoft.ServiceBus.Notifications;

6. Nella classe **Program** aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version="1.0" encoding="utf-8"?>" +
                "<wp:Notification xmlns:wp="WPNotification">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	Assicurarsi di sostituire il segnaposto relativo al nome dell'hub con il nome dell'hub di notifica visualizzato nella scheda **Hub di notifica** del portale. Sostituire inoltre il segnaposto relativo alla stringa di connessione con la stringa di connessione denominata **DefaultFullSharedAccessSignature** ottenuta nella sezione Configurazione dell'hub di notifica.

	>[AZURE.NOTE]Assicurarsi di usare la stringa di connessione con accesso **Full**, non con accesso **Listen**. La stringa con accesso di tipo Attesa non dispone infatti delle autorizzazioni per l'invio di notifiche.

4. Aggiungere quindi la riga seguente nel metodo Main:

         SendNotificationAsync();
		 Console.ReadLine();

5. Con l'emulatore di Windows Phone in esecuzione e l'app chiusa, impostare il progetto di applicazione console come progetto di avvio predefinito, quindi premere F5 key per eseguire l'app.

	Verrà ricevuta una notifica di tipo avviso popup. Se si tocca il bannner dell'avviso popup, verrà caricata l'app.

Tutti i possibili payload sono disponibili negli argomenti relativi al [catalogo degli avvisi popup] e al [catalogo dei riquadri] su MSDN.

##Passaggi successivi

In questo semplice esempio le notifiche verranno trasmesse a tutti i dispositivi Windows Phone 8. Per rivolgersi a utenti specifici, fare riferimento all'esercitazione [Usare Hub di notifica per inviare notifiche push agli utenti]. Se si desidera segmentare gli utenti per gruppi di interesse, consultare [Uso di Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'uso di Hub di notifica, vedere la pagina delle [linee guida su Hub di notifica].



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
[portale di gestione di Azure]: https://manage.windowsazure.com/
[linee guida su Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[modalità con autenticazione del Servizio di notifica Push di Microsoft]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Usare Hub di notifica per inviare notifiche push agli utenti]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Uso di Hub di notifica per inviare le ultime notizie]: notification-hubs-windows-phone-send-breaking-news.md
[catalogo degli avvisi popup]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[catalogo dei riquadri]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Esercitazione su Hub di notifica - WP Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp


<!---HONumber=52-->
 