<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Get started with notification hubs" pageTitle="Get started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

Introduzione ad Hub di notifica
===============================

[Windows Store C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/it-it/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/it-it/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/it-it/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/it-it/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/it-it/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/it-it/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

In questo argomento viene illustrato come utilizzare Hub di notifica di Azure per inviare notifiche push a un'applicazione per Windows Store. In questa esercitazione si creerà un'app di Windows Store vuota che riceve notifiche push tramite Servizi notifica Push Windows. Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi che eseguono l'app tramite l'hub di notifica.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1.  [Registrazione dell'app per le notifiche push](#register)
2.  [Configurazione dell'hub di notifica](#configure-hub)
3.  [Connessione dell'app all'hub di notifica](#connecting-app)
4.  [Invio di notifiche dal back-end](#send)

In questa esercitazione viene illustrato uno scenario di trasmissione semplice tramite Hub di notifica. Seguire anche l'esercitazione successiva per imparare a utilizzare gli hub di notifica destinati a gruppi di dispositivi e a utenti specifici. Per completare questa esercitazione, è necessario disporre di:

-   Microsoft Visual Studio 2012 Express per Windows 8
-   Account di Windows Store attivo

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub notifica relative ad app di Windows Store.

**Nota**

Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

Registrazione dell'appRegistrazione dell'app di Windows Store
-------------------------------------------------------------

Per inviare notifiche push ad app di Windows Store da Servizi mobili, è necessario inviare l'app a Windows Store e quindi configurare l'hub di notifica per l'integrazione con Servizi notifica Push Windows.

1.  Se l'app non è ancora stata registrata, passare alla pagina di [invio di app](http://go.microsoft.com/fwlink/p/?LinkID=266582) nel Centro per sviluppatori di app di Windows Store, accedere con il proprio account Microsoft e quindi fare clic su **Nome app**.

	![][0]

2.  Immettere un nome per l'app in **Nome app**, fare clic su **Riserva nome applicazione** e quindi su **Salva**.

	![][1]

	Verrà creata una nuova registrazione a Windows Store per l'app.

3.  In Visual Studio 2012 Express per Windows 8 creare un nuovo progetto Visual C\# Windows Store utilizzando il modello **Applicazione vuota**.

	![][2]

4.  In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto, scegliere **Store** e quindi fare clic su **Associa applicazione a Store**.

	![][3]

	Verrà visualizzata la procedura guidata **Associa l'applicazione con Windows Store**.

5.  Nella procedura guidata fare clic su **Accedi** e quindi accedere con il proprio account Microsoft.

6.  Fare clic sull'app registrata nel passaggio 2, fare clic su **Avanti** e quindi su **Associa**.

	![][4]

	Le necessarie informazioni di registrazione a Windows Store verranno aggiunte al manifesto dell'applicazione.    

7.  Nella pagina di Windows Dev Center per la nuova app fare clic su **Servizi**.

	![][5] 

8.  Nella pagina **Servizi** fare clic su **Sito di servizi Live** in **Servizi mobili di Azure**.

	![][17]

9.  Fare clic su **Autenticazione del servizio** e prendere nota dei valori di **Segreto client** e **Identificatore di sicurezza del pacchetto (SID)**.

	![][6]

	<div class="dev-callout"\><b>Nota sulla sicurezza</b>

    <p>Il segreto client e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri né distribuirli con l'app.</p>
    </div>

Configurazione dell'hub di notificaConfigurazione dell'hub di notifica
----------------------------------------------------------------------

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/) e fare clic su **NEW** nella parte inferiore della schermata.

2.  Fare clic su **App Services**, selezionare **Service Bus**, **Notification Hub**, quindi **Quick Create**.

	![][7]

3.  Digitare un nome per l'hub di notifica, selezionare l'area desiderata, quindi fare clic su **Create a new Notification Hub**.

	![][8]

4.  Fare clic sullo spazio dei nomi appena creato (in genere ***nome hub di notifica*-ns**), quindi fare clic sulla scheda **Configure** nella parte superiore.

	![][9]

5.  Fare clic sulla scheda **Notification Hubs** nella parte superiore, quindi fare clic sull'hub di notifica appena creato.

	![][10]

6.  Fare clic sulla scheda **Configura** nella parte superiore, immettere i valori di **Segreto client** e **Package SID** ottenuti da Servizi notifica Push Windows nella sezione precedente, quindi fare clic su **Salva**.

	![][11]

7.  Selezionare la scheda **Dashboard** nella parte superiore e quindi fare clic su **Connection Information**. Prendere nota delle due stringhe di connessione.

	![][12]

L'hub di notifica è ora configurato per l'uso con Servizi notifica Push Windows e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche.

Connessione dell'appConnessione dell'app all'hub di notifica
------------------------------------------------------------

1.  Aggiungere un riferimento alla libreria di Messaggistica di Azure per Windows Store utilizzando il [pacchetto NuGet WindowsAzure.Messaging.Managed](http://nuget.org/packages/WindowsAzure.Messaging.Managed/). Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi scegliere **Gestione pacchetti libreria** e infine **Console di Gestione pacchetti**. Quindi nella finestra della console digitare:

         Install-Package WindowsAzure.Messaging.Managed

    e premere **INVIO**.

2.  Aprire il file App.xaml.cs e aggiungere le istruzioni `using` seguenti:

         using Windows.Networking.PushNotifications;
         using Microsoft.WindowsAzure.Messaging;
         using Windows.UI.Popups;

3.  Aggiungere il codice seguente al file App.xaml.cs nella classe `App`: Assicurarsi di sostituire il segnaposto relativo al nome dell'hub con il nome dell'hub di notifica visualizzato nella scheda **Hub di notifica** del portale, ad esempio **mynotificationhub2** nell'esempio precedente:

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

    Assicurarsi di inserire il nome dell'hub e la stringa di connessione denominata **DefaultListenSharedAccessSignature** e ottenuta nella sezione precedente. Questo codice consente di recuperare il valore di ChannelURI per l'app da Servizi notifica Push Windows e quindi di registrarlo con l'hub di notifica.

4.  All'inizio del gestore dell'evento **OnLaunched** in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo **InitNotificationsAsync**:

         InitNotificationsAsync();

    In questo modo si garantisce che il valore di ChannelURI venga registrato nell'hub di notifica a ogni avvio dell'applicazione.

5.  Premere **F5** per eseguire l'app. Verrà visualizzata una finestra di dialogo popup con la chiave di registrazione.

	![][19]

Invio di notificheInvio di notifiche dal back-end
-------------------------------------------------

È possibile utilizzare Hub di notifica per inviare notifiche da qualsiasi back-end tramite l'[interfaccia REST](http://msdn.microsoft.com/it-it/library/windowsazure/dn223264.aspx). In questa esercitazione vengono inviate notifiche con un'applicazione console .NET e con un servizio mobile tramite uno script node.

Per inviare notifiche tramite un'app .NET:

1.  Con la soluzione precedente ancora aperta in Visual Studio, fare doppio clic su **Package.appxmanifest** in Esplora soluzioni per aprirlo nell'editor di Visual Studio.

2.  Scorrere in basso fino a **Tutti gli asset delle immagini** e fare clic su **Logo badge**. In **Notifiche** impostare **Popup supportati** su **Sì**:

	![][18]

	Scegliere **Salva tutto** dal menu **File**.

3.  Creare ora una nuova applicazione console in Visual C\#:

	![][13]

4.  Aggiungere un riferimento ad Azure Service Bus SDK con il [pacchetto NuGet WindowsAzure.ServiceBus](http://nuget.org/packages/WindowsAzure.ServiceBus/). Nel menu principale di Visual Studio fare clic su **Strumenti**, quindi scegliere **Gestione pacchetti libreria** e infine **Console di Gestione pacchetti**. Quindi nella finestra della console digitare quanto segue:

         Install-Package WindowsAzure.ServiceBus

    e premere **INVIO**.

5.  Aprire il file Program.cs e aggiungere l'istruzione `using` seguente:

         using Microsoft.ServiceBus.Notifications;

6.  Nella classe `Program` aggiungere il metodo seguente. Assicurarsi di sostituire il segnaposto relativo al nome dell'hub con il nome dell'hub di notifica visualizzato nella scheda **Hub di notifica** del portale:

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
             await hub.SendWindowsNativeNotificationAsync(toast);
         }

	Assicurarsi di inserire il nome dell'hub e la stringa di connessione denominata **DefaultFullSharedAccessSignature** e ottenuta nella sezione "Configurazione dell'hub di notifica". Si noti che questa è la stringa di connessione con accesso **Full**, non con accesso **Listen**.

7.  Aggiungere quindi le righe seguenti nel metodo `Main`:

          SendNotificationAsync();
          Console.ReadLine();

8.  Premere **F5** per eseguire l'app. Si dovrebbe ricevere una notifica di tipo avviso popup.

	![][14]

Tutti i possibili payload sono disponibili nel [catalogo degli avvisi popup](http://msdn.microsoft.com/it-it/library/windows/apps/hh761494.aspx), nel [catalogo dei riquadri](http://msdn.microsoft.com/it-it/library/windows/apps/hh761491.aspx) e nella [panoramica dei badge](http://msdn.microsoft.com/it-it/library/windows/apps/hh779719.aspx) su MSDN.

Per inviare una notifica tramite un servizio mobile, eseguire quanto descritto in [Introduzione a Servizi mobili](/en-us/develop/mobile/tutorials/get-started/#create-new-service), quindi eseguire le operazioni seguenti:

1.  Accedere al [portale di gestione di Azure](https://manage.windowsazure.com/) e fare clic sul servizio mobile.

2.  Selezionare la scheda **Scheduler** nella parte superiore.

	![][15]

3.  Creare un nuovo processo pianificato, immettere un nome, quindi scegliere **Su richiesta**.

	![][16]

4.  Una volta creato il processo, fare clic sul relativo nome. Fare quindi clic sulla scheda **Script** nella barra superiore.

5.  Inserire lo script seguente all'interno della funzione dell'utilità di pianificazione. Assicurarsi di sostituire i segnaposto con il nome dell'hub di notifica e la stringa di connessione associata a *DefaultFullSharedAccessSignature* ottenuti nel passaggio precedente. Al termine, fare clic su **Salva** sulla barra inferiore.

         var azure = require('azure');
         var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
         notificationHubService.wns.sendToastText01(
             null,
             {
                 text1: 'Hello from Mobile Services!!!'
             },
             function (error) {
                 if (!error) {
                     console.warn("Notification successful");
                 }
         });

6.  Fare clic su **Run Once** sulla barra inferiore. Si dovrebbe ricevere una notifica di tipo avviso popup.

Passaggi successivi
-------------------

In questo semplice esempio le notifiche di trasmissione sono state inviate a tutti i dispositivi Windows. Per rivolgersi a utenti specifici, fare riferimento all'esercitazione [Utilizzo di Hub di notifica per inviare notifiche push agli utenti](/en-us/manage/services/notification-hubs/notify-users-aspnet). Se si desidera segmentare gli utenti per gruppi di interesse, vedere [Utilizzo di Hub di notifica per inviare le ultime notizie](/en-us/manage/services/notification-hubs/breaking-news-dotnet). Per ulteriori informazioni sull'utilizzo di Hub di notifica, vedere la pagina delle [linee guida su Hub di notifica](http://msdn.microsoft.com/it-it/library/jj927170.aspx) e quella relativa alle [procedure di Hub di notifica per Windows Store](http://msdn.microsoft.com/it-it/library/jj927172.aspx).

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-win8-app.png
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