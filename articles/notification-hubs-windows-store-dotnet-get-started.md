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
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>

# Introduzione ad Hub di notifica

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##Informazioni generali

In questo argomento viene illustrato come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Windows Store o Windows Phone 8.1 (non Silverlight). Se si sviluppano app per Windows Phone 8.1 Silverlight, fare riferimento alla versione [Windows Phone](notification-hubs-windows-phone-get-started.md) . 
In questa esercitazione verrà creata un'app di Windows Store vuota che riceve notifiche push tramite Servizi notifica Push Windows (WNS). Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

Questa esercitazione illustra uno scenario di trasmissione semplice tramite Hub di notifica. Seguire anche l'esercitazione successiva per imparare a usare gli hub di notifica destinati a gruppi di dispositivi e a utenti specifici. 


##Prerequisiti

Per completare questa esercitazione, è necessario disporre di:

+ Microsoft Visual Studio Express 2013 per Windows con Update 2<br/>Questa versione di Visual Studio è necessaria per creare un progetto di app universale. Se si desidera solo creare un'app di Windows Store, è necessario Visual Studio Express 2012 per Windows 8.

+ Account di Windows Store attivo

+ Un account Azure attivo. <br/>Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-it%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F).

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app di Windows Store. 

##Registrare l'app di Windows Store

Per inviare notifiche push ad app di Windows Store da Servizi mobili, è necessario inviare l'app a Windows Store, quindi configurare l'hub di notifica per l'integrazione con Servizi notifica Push Windows.

1. Se l'app non è ancora stata registrata, passare alla <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">pagina di invio di app</a> nel Centro per sviluppatori di app di Windows Store, accedere con il proprio account Microsoft e quindi fare clic su **Nome app**.

   	![][0]

2. Immettere un nome per l'app in **Nome app**, fare clic su **Riserva nome dell'app** e quindi su **Salva**.

   	![][1]

   	Verrà creata una nuova registrazione a Windows Store per l'app.

3. In Visual Studio creare un nuovo progetto di app di Windows Store in Visual C# usando il modello **Applicazione vuota**.

   	![][2]

4. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di app di Windows Store, scegliere **Store**, quindi fare clic su **Associa applicazione a Store**. 

   	![][3]

   	Viene visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.

5. Nella procedura guidata fare clic su **Accedi**, quindi accedere con il proprio account Microsoft.

6. Fare clic sull'app registrata nel passaggio 2, fare clic su **Avanti** e quindi su **Associa**.

   	![][4]

   	Le necessarie informazioni di registrazione a Windows Store verranno aggiunte al manifesto dell'applicazione. 

7. (Facoltativo) Ripetere i passaggi da 4 a 6 per il progetto di app di Windows Phone Store.  

7. Nella pagina di Windows Dev Center per la nuova app fare clic su **Servizi**. 

   	![][5] 

8. Nella pagina **Servizi** fare clic su **Sito di servizi Live** nel paragrafo **Servizi mobili di Microsoft Azure**.

   	![][17]

9. Nella scheda **Impostazioni app** prendere nota dei valori specificati nei campi **Chiave privata client** e **Identificatore di sicurezza del pacchetto (SID)**. 

   	![][6]

 	> [AZURE.NOTE] **Nota sulla sicurezza**
	Il segreto client e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri utenti né distribuirli con l'app.

##Configurare l'hub di notifica

1. Accedere al [portale di gestione di Azure] e fare clic su **NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, selezionare **Bus di servizio**, **Hub di notifica**, quindi **Creazione rapida**.

   	![][7]

3. Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Crea un nuovo hub di notifica**.

   	![][8]

4. Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic sulla scheda **Configura** nella parte superiore.

   	![][9]

5. Fare clic sulla scheda **Hub di notifica** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

   	![][10]

6. Fare clic sulla scheda **Configura** nella parte superiore, immettere i valori di **Chiave privata client** e **SID pacchetto**  ottenuti da Servizi notifica Push Windows nella sezione precedente, quindi fare clic su **Salva**.

   	![][11]

7. Selezionare la scheda **Dashboard** nella parte superiore, quindi fare clic sul pulsante **Informazioni di connessione** nella parte inferiore della pagina. Prendere nota delle due stringhe di connessione.

   	![][12]

L'hub di notifica è ora configurato per l'uso con Servizi notifica Push Windows e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche.

##Connettere l'app all'hub di notifica

1. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione, quindi scegliere **Gestisci pacchetti NuGet**. 

	Verrà visualizzata la finestra di dialogo Gestisci pacchetti NuGet.

2. Cercare  `WindowsAzure.Messaging.Managed` e fare clic su **Installa**, selezionare tutti i progetti inclusi nella soluzione e accettare le condizioni per l'uso. 

	![][20]

	In tutti i progetti viene scaricato, installato e aggiunto un riferimento alla libreria di messaggistica di Azure usando il pacchetto <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet</a>. 

3. Aprire il file di progetto App.xaml.cs e aggiungere le seguenti istruzioni  `using`:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;

	In un progetto universale questo file si trova nella cartella  `<project_name>.Shared`.

4. In App.xaml.cs aggiungere inoltre alla classe **App** la seguente definizione del metodo **InitNotificationsAsync**:
	
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

    >[AZURE.NOTE]Assicurarsi di sostituire il segnaposto "nome hub" con il nome dell'hub di notifica visualizzato nel portale nella scheda **Hub di notifica** (ad esempio, **mynotificationhub2** nell'esempio precedente). Sostituire inoltre il segnaposto relativo alla stringa di connessione con la stringa di connessione **DefaultListenSharedAccessSignature** ottenuta nella sezione precedente.
    
5. All'inizio del gestore eventi **OnLaunched** in App.xaml.cs, aggiungere la seguente chiamata al nuovo metodo **InitNotificationsAsync**:

        InitNotificationsAsync();

    In questo modo si garantisce che il valore di ChannelURI venga registrato nell'hub di notifica a ogni avvio dell'applicazione.

6. In Esplora soluzioni fare doppio clic sul file **Package.appxmanifest** dell'app di Windows Store e in **Notifiche** impostare **Popup supportati** su **Sì**:

   	![][18]

   	Scegliere **Salva tutto** dal menu **File**.

7. (Facoltativo) Ripetere il passaggio precedente nel progetto di app di Windows Phone Store.

8. Premere **F5** per eseguire l'app. Verrà visualizzata una finestra di dialogo popup con la chiave di registrazione.
   
   	![][19]

9. (Facoltativo) Ripetere il passaggio precedente per eseguire l'altro progetto.

L'app è ora pronta per ricevere notifiche di tipo avviso popup.

##Inviare notifiche dal back-end

È possibile inviare notifiche usando gli hub di notifica da qualsiasi back-end tramite l'<a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">interfaccia REST</a>. In questa esercitazione vengono inviate notifiche con un'applicazione console .NET. Per un esempio di come inviare notifiche da un back-end di Servizi mobili di Azure integrato con Hub di notifica, vedere **Introduzione alle notifiche push in Servizi mobili** ([back-end .NET](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md) | [back-end JavaScript](mobile-services-javascript-backend-windows-store-dotnet-get-started-push.md)).  Per un esempio relativo all'invio di notifiche tramite le API REST, vedere **Come usare Hub di notifica da Java/PHP** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. Fare clic con il pulsante destro del mouse sulla soluzione, scegliere **Aggiungi** e **Nuovo progetto**, quindi in **Visual C#** fare clic su **Windows** e **Applicazione console**, infine su **OK**. 

   	![][13]

	Una nuova applicazione console Visual C# verrà aggiunta alla soluzione. Questa operazione può essere eseguita anche in una soluzione separata. 

4. In Visual Studio fare clic su **Strumenti**, quindi selezionare **Gestione pacchetti Nuget** e infine **console di Gestione pacchetti**. 

	In questo modo viene visualizzata la console di Gestione pacchetti in Visual Studio.

6. Nella finestra della console di Gestione pacchetti impostare **Progetto predefinito** sul nuovo progetto di applicazione console, quindi eseguire il seguente comando nella finestra della console:

        Install-Package WindowsAzure.ServiceBus
    
	Verrà aggiunto un riferimento ad Azure Service Bus SDK con il <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">pacchetto WindowsAzure.ServiceBus NuGet</a>. 

5. Aprire il file Program.cs e aggiungere la seguente istruzione `using`:

        using Microsoft.ServiceBus.Notifications;

6. Nella classe **Program** aggiungere il metodo seguente:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	Assicurarsi di sostituire il segnaposto relativo al nome dell'hub con il nome dell'hub di notifica visualizzato nella scheda **Hub di notifica** del portale. Sostituire anche il segnaposto relativo alla stringa di connessione con la stringa di connessione denominata **DefaultFullSharedAccessSignature** ottenuta nella sezione "Configurare l'hub di notifica". 

	>[AZURE.NOTE]Assicurarsi di usare la stringa di connessione con accesso **Full**, non con accesso **Listen**. La stringa con accesso di tipo Listen non è autorizzata all'invio di notifiche.

7. Aggiungere quindi le righe seguenti nel metodo **Main**:

         SendNotificationAsync();
		 Console.ReadLine();

8. Fare clic con il pulsante destro del mouse sul progetto di applicazione console in Visual Studio e fare clic su **Imposta come progetto di avvio** per impostarlo come progetto di avvio. Premere quindi **F5** per eseguire l'applicazione. 

   	![][14]

	Tutti i dispositivi registrati riceveranno una notifica di tipo avviso popup. Facendo clic o toccando il banner dell'avviso popup l'app viene caricata.

Per trovare tutti i payload supportati, è possibile consultare gli argomenti relativi al [catalogo degli avvisi popup], al [catalogo dei riquadri] e alla [panoramica delle notifiche] su MSDN.

##Passaggi successivi

In questo semplice esempio le notifiche di trasmissione sono state inviate a tutti i dispositivi Windows. Per rivolgersi a utenti specifici, fare riferimento all'esercitazione [Uso di Hub di notifica per inviare notifiche push agli utenti]. Se si desidera segmentare gli utenti per gruppi di interesse, vedere [Uso di Hub di notifica per inviare le ultime notizie]. Per altre informazioni sull'uso di Hub di notifica, vedere la [pagina delle linee guida su Hub di notifica].



<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
[12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Portale di gestione di Azure]: https://manage.windowsazure.com/
[portale di gestione di Azure]: https://manage.windowsazure.com/
[Informazioni aggiuntive su Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx
[pagina delle linee guida su Hub di notifica]: http://msdn.microsoft.com/library/jj927170.aspx


[Uso di Hub di notifica per inviare notifiche push agli utenti]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Uso di Hub di notifica per inviare le ultime notizie]: notification-hubs-windows-store-dotnet-send-breaking-news.md

[catalogo degli avvisi popup]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[catalogo dei riquadri]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[panoramica dei badge]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx
[panoramica delle notifiche]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx

<!--HONumber=49-->