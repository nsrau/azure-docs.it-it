<properties 
	pageTitle="Aggiungere notifiche push all'app universale di Windows Runtime 8.1 | App per dispositivi mobili di Azure" 
	description="Informazioni su come usare le app per dispositivi mobili del servizio app di Azure e gli hub di notifica di Azure per inviare notifiche push all'app di Windows." 
	services="app-service\mobile,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/14/2015" 
	ms.author="glenga"/>

# Aggiungere notifiche push all'app universale di Windows Runtime 8.1

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Panoramica

Questo argomento descrive come inviare notifiche push a un'app universale di Windows Runtime 8.1 tramite le app per dispositivi mobili del servizio app di Azure e gli hub di notifica di Azure. In questo scenario, quando viene aggiungo un nuovo elemento, il back-end dell'app per dispositivi mobili invia una notifica push a tutte le app di Windows registrate al servizio di notifica Windows.

Questa esercitazione è basata sulla guida introduttiva delle app per dispositivi mobili del servizio app. Prima di iniziare questa esercitazione, è necessario completare l'esercitazione relativa alla guida introduttiva [Creare un'app di Windows](../app-service-mobile-windows-store-dotnet-get-started.md). Se non si usa il progetto server di avvio rapido scaricato, è necessario aggiungere il pacchetto di estensione di notifica push al progetto. Per altre informazioni sui pacchetti di estensione server, vedere l'articolo relativo all'[utilizzo dell'SDK del server back-end .NET per app per dispositivi mobili di Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

##Prerequisiti

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un [account di Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) attivo.
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* Completare l'[esercitazione della guida introduttiva](../app-service-mobile-windows-store-dotnet-get-started.md).  


##<a name="create-hub"></a>Creare un hub di notifica

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Registrare l'app per le notifiche push

Per potere inviare notifiche push alle app di Windows da Azure, è prima di tutto necessario inviare l'app a Windows Store. Sarà quindi possibile configurare il progetto server per l'integrazione con il servizio di notifica Windows.

1. In Esplora soluzioni di Visual Studio fare clic con il pulsante destro del mouse sul progetto app di Windows Store, quindi scegliere **Store** > **Associa applicazione a Store**. 

    ![Associa l’app con Windows Store](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-win8-app.png)
    
2. Nella procedura guidata fare clic su **Avanti**, accedere con l'account Microsoft, immettere un nome per l'app in **Riserva un nuovo nome dell’app** e quindi fare clic su **Riserva**.

3. Dopo la creazione della registrazione dell'app, selezionare il nuovo nome dell'app, fare clic su **Avanti** e quindi su **Associa**. Le informazioni di registrazione a Windows Store necessarie verranno aggiunte al manifesto dell'applicazione.

7. Ripetere i passaggi 1 e 3 per il progetto app di Windows Phone Store usando la stessa registrazione creata in precedenza per l'app di Windows Store.

7. Passare a [Windows Dev Center](https://dev.windows.com/it-IT/overview), accedere con l'account Microsoft, fare clic sulla nuova registrazione di app in **App personali**, quindi espandere **Servizi** > **Notifiche push**.

8. Nella pagina **Notifiche push** fare clic su **Sito di servizi Live** in **Servizi mobili di Microsoft Azure**.

9. Nella scheda **Impostazioni app** annotare i valori di **Chiave privata client** e **SID di pacchetto**.

    ![Impostazione dell’app nel centro per sviluppatori](./media/app-service-mobile-windows-store-dotnet-get-started-push/mobile-services-win8-app-push-auth.png)

    > [AZURE.IMPORTANT]Il segreto client e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri utenti né distribuirli con l'app.

##Configurare il servizio per app per dispositivi mobili per l'invio di richieste push

1. Accedere al [portale di Azure], selezionare **Sfoglia** > **App per dispositivi mobili** > app > **Servizi notifica Push**.

2. In **Servizio di notifica Windows** immettere la **Chiave di sicurezza** (chiave privata client) e il **SID di pacchetto** ottenuti dal sito di servizi Live, quindi fare clic su **Salva**.

Il back-end dell'app per dispositivi mobili è ora configurato per l'uso del Servizio di notifica Windows.

##<a id="update-service"></a>Aggiornare il server per l'invio di notifiche push

Ora che le notifiche push sono abilitate nell'app, è necessario aggiornare il back-end dell'app per l'invio delle notifiche push.

1. In Visual Studio fare clic con il pulsante destro del mouse sul progetto server, quindi scegliere **Gestisci pacchetti NuGet**, cercare `Microsoft.Azure.NotificationHubs` e infine fare clic su **Installa**. Verrà installata la libreria client dell'Hub di notifica.

3. Nel progetto server aprire **Controller** > **TodoItemController.cs**, quindi aggiungere le istruzioni using seguenti:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. Nel metodo **PostTodoItem** aggiungere il codice seguente dopo la chiamata a **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
			this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

		// Define a WNS payload
		var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" 
                                + item.Text + @"</text></binding></visual></toast>";

        try
        {
			// Send the push notification and log the results.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Questo codice indica all'hub di notifica di inviare una notifica push dopo l'inserimento di un nuovo elemento.


## <a name="publish-the-service"></a>Pubblicare il back-end mobile in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##<a id="update-service"></a>Aggiungere notifiche push all'app

1. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione, quindi scegliere **Gestisci pacchetti NuGet**. 

    Verrà visualizzata la finestra di dialogo Gestisci pacchetti NuGet.

2. Cercare l'SDK per client delle app per dispositivi mobili del servizio app e fare clic su **Installa**, selezionare tutti i progetti client nella soluzione e accettare le condizioni per l'uso.

    Verrà scaricato, installato e aggiunto un riferimento in tutti i progetti client per la libreria push per dispositivi mobili di Azure per Windows.

3. Aprire il file di progetto condiviso **App.xaml.cs** e aggiungere le istruzioni `using` seguenti:

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

4. Nello stesso file aggiungere la seguente definizione del metodo **InitNotificationsAsync** alla classe **App**:
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Questo codice consente di recuperare il valore di ChannelURI per l'app da Servizi notifica Push Windows e quindi di registrarlo con l'app per dispositivi mobili del servizio app.
    
5. All'inizio del gestore eventi **OnLaunched** nel file **App.xaml.cs** aggiungere il modificatore **async** alla definizione del metodo, quindi aggiungere la chiamata seguente al nuovo metodo **InitNotificationsAsync**, come illustrato di seguito:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    In tal modo si garantirà che il valore di ChannelURI temporaneo venga registrato ogni volta che l'applicazione viene avviata.

6. In Esplora soluzioni fare doppio clic sul file **Package.appxmanifest** dell'app di Windows Store e in **Notifiche** impostare **Popup supportati** su **Sì**.

    Scegliere **Salva tutto** dal menu **File**.

7. Ripetere il passaggio precedente nel progetto di app di Windows Phone Store.

L'app è ora pronta per ricevere notifiche di tipo avviso popup.

##<a id="test"></a>Testare le notifiche push nell'app

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

<!-- Anchors. -->

<!-- URLs. -->
[portale di Azure]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=Nov15_HO2-->