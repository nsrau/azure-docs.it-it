<properties 
	pageTitle="Aggiungere notifiche push all'app universale di Windows tramite il servizio app di Azure" 
	description="Informazioni su come usare il servizio app di Azure per inviare notifiche push all'app universale di Windows." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="yuaxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="yuaxu"/>

# Aggiungere notifiche push all'app di Windows Store

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

Questo argomento descrive come inviare notifiche push a un'app universale di Windows da un back-end .NET tramite il servizio app di Azure. Al termine dell'esercitazione si potranno inviare notifiche push dal back-end .NET a tutte le app di Windows universale registrate dopo l'inserimento di un record.

In questa esercitazione vengono descritte le operazioni di base per abilitare le notifiche push:

1. [Registrare l'app per le notifiche push](#register)
2. [Configurare il servizio per app mobili per l'invio di notifiche push](#configure)
3. [Aggiornare il servizio per l'invio di notifiche push](#update-service)
4. [Aggiungere notifiche push all'app](#add-push)
5. [Testare le notifiche push nell'app](#test)

Questa esercitazione è basata sulla guida introduttiva delle app mobili del servizio app. Prima di iniziare questa esercitazione, è necessario completare le procedure illustrate in [Introduzione alle app mobili del servizio app].

Per completare l'esercitazione, sono necessari gli elementi seguenti:

* Un [account di Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) attivo.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>.

##<a id="register"></a>Registrare l'app per le notifiche push

Per inviare notifiche push ad app di Windows universale con il servizio app di Azure, è necessario inviare l'app a Windows Store, quindi configurare le credenziali del servizio di notifica push dell'app mobile per l'integrazione con Servizi notifica push Windows.

1. Se l'app non è ancora stata registrata, passare alla <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">pagina Invia un'app</a>nel Centro per sviluppatori di app di Windows Store, accedere con il proprio account Microsoft e quindi fare clic su **Nome app**.

    ![][0]

2. Immettere un nome per l'app in **Nome app**, fare clic su **Riserva nome dell'app** e quindi su **Salva**.

    ![][1]

    Verrà creata una nuova registrazione a Windows Store per l'app.

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

9. Nella scheda **Impostazioni app** prendere nota dei valori specificati nei campi **Segreto client** e **Identificatore di sicurezza del pacchetto (SID)**. 

    ![][6]

    > [AZURE.NOTE] **Nota sulla sicurezza**
    Il segreto client e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri utenti né distribuirli con l'app.

##<a id="configure"></a>Configurare il servizio per app mobili per l'invio di richieste push

1. Accedere al [portale di anteprima di Azure], selezionare **Sfoglia**, **App mobile**, quindi fare clic sull'app desiderata. Fare quindi clic su Servizi di notifica push.

2. In Servizio di notifica Windows, immettere il **Segreto client** e l'**Identificatore di sicurezza del pacchetto (SID)**, quindi salvare.

L'app mobile del servizio app è ora configurata per funzionare con i Servizi notifica Push Windows.

<!-- URLs. -->
[Portale di anteprima di Azure]: https://portal.azure.com/

##<a id="update-service"></a>Aggiornare il servizio per l'invio di notifiche push

Ora che le notifiche push sono abilitate nell'app, è necessario aggiornare il back-end dell'app per l'invio delle notifiche push. 

1. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione, quindi scegliere **Gestisci pacchetti NuGet**.

2. Cercare **Microsoft.Azure.NotificationHubs** e fare clic su **Installa** per tutti i progetti nella soluzione.

3. In Esplora soluzioni di Visual Studio espandere la cartella **Controller** nel progetto di back-end mobile. Aprire TodoItemController.cs. Aggiungere le istruzioni `using` seguenti all'inizio del file:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Aggiungere il frammento seguente al metodo `PostTodoItem` dopo la chiamata **InsertAsync**:  

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

        // windows payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + item.Text + @"</text></binding></visual></toast>";

        try
        {
            await Hub.SendWindowsNativeNotificationAsync(windowsToastPayload);
        }
        catch (System.Exception ex)
        {
            throw ex;
        }

    Questo codice indicherà all'hub di notifica associato a questa app mobile di inviare una notifica push dopo l'inserimento di un elemento ToDo.


<h2><a name="publish-the-service"></a>Pubblicare il back-end mobile in Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##<a id="update-service"></a>Aggiungere notifiche push all'app

1. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione, quindi scegliere **Gestisci pacchetti NuGet**. 

    Verrà visualizzata la finestra di dialogo Gestisci pacchetti NuGet.

2. Cercare l'SDK per client delle app mobili del servizio app e fare clic su **Installa**, selezionare tutti i progetti nella soluzione e accettare le condizioni per l'utilizzo. 

    Verrà quindi scaricato e installato il pacchetto e verrà aggiunto un riferimento in tutti i progetti alla libreria push mobile per Windows di Azure. 

3. Aprire il file di progetto **App.xaml.cs** e aggiungere le seguenti istruzioni `using`:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.MobileServices;

    In un progetto universale, questo file si trova nella cartella `<project_name>.Shared`.

4. Nello stesso file aggiungere la seguente definizione del metodo **InitNotificationsAsync** alla classe **App**:
    
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            
            var result = MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Questo codice consente di recuperare il valore di ChannelURI per l'app da Servizi notifica Push Windows e quindi di registrarlo con l'app mobile del servizio app.
    
5. All'inizio del gestore eventi **OnLaunched** in **App.xaml.cs**, aggiungere la seguente chiamata al nuovo metodo **InitNotificationsAsync**:

        InitNotificationsAsync();

    In tal modo si garantirà che il valore di ChannelURI temporaneo venga registrato ogni volta che l'applicazione viene avviata.

6. In Esplora soluzioni fare doppio clic sul file **Package.appxmanifest** dell'app di Windows Store e in **Notifiche** impostare **Popup supportati** su **Sì**:

    Scegliere **Salva tutto** dal menu **File**.

7. (Facoltativo) Ripetere il passaggio precedente nel progetto di app di Windows Phone Store.

8. Premere **F5** per eseguire l'app.

L'app è ora pronta per ricevere notifiche di tipo avviso popup.

##<a id="test"></a>Testare le notifiche push nell'app

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-windows-universal-test-push-preview](../includes/app-service-mobile-dotnet-backend-windows-universal-test-push-preview.md)]

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png

<!-- URLs. -->
[Pagina Invia un'app]: http://go.microsoft.com/fwlink/p/?LinkID=266582

<!--HONumber=49-->