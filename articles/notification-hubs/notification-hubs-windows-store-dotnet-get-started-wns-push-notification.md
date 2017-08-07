---
title: Introduzione ad Hub di notifica per le app della piattaforma UWP (Universal Windows Platform) |Documentazione Microsoft
description: Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un'applicazione della piattaforma UWP (Universal Windows Platform).
services: notification-hubs
documentationcenter: windows
author: ysxu
manager: erikre
editor: erikre
ms.assetid: cf307cf3-8c58-4628-9c63-8751e6a0ef43
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 10/03/2016
ms.author: yuaxu
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: 9b50f1cca81348b69f7ff2d702c6c72871afe0a0
ms.contentlocale: it-it
ms.lasthandoff: 08/02/2017

---
# <a name="getting-started-with-notification-hubs-for-windows-universal-platform-apps"></a>Introduzione ad Hub di notifica per le app della piattaforma UWP (Universal Windows Platform)
[!INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

## <a name="overview"></a>Panoramica
Questa esercitazione illustra come usare Hub di notifica di Azure per inviare notifiche push a un'app della piattaforma UWP (Universal Windows Platform).

In questa esercitazione si creerà un'app di Windows Store vuota che riceve notifiche push tramite Servizi notifica Push Windows (WNS). Al termine, sarà possibile usare l’hub di notifica per trasmettere le notifiche push a tutti i dispositivi che eseguono l'app.

## <a name="before-you-begin"></a>Prima di iniziare
[!INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

Il codice completo per questa esercitazione è disponibile su GitHub [qui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/GetStartedWindowsUniversal).

## <a name="prerequisites"></a>Prerequisiti
Per completare questa esercitazione, è necessario disporre di:

* [Microsoft Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs) o versione successiva
* [Strumenti per lo sviluppo di app di Windows universale](https://msdn.microsoft.com/windows/uwp/get-started/get-set-up)
* Account Azure attivo  <br/>Se non si dispone di un account Azure, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-store-dotnet-get-started%2F).
* Account di Windows Store attivo

Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Hub di notifica relative ad app della piattaforma UWP (Universal Windows Platform).

## <a name="register-your-app-for-the-windows-store"></a>Registrare l'app di Windows Store
Per inviare notifiche push ad app UWP è necessario associare l'app a Windows Store e quindi configurare l'hub di notifica per l'integrazione con Servizi notifica Push Windows.

1. Se l'app non è ancora stata registrata, passare a [Windows Dev Center](https://dev.windows.com/overview), accedere con il proprio account Microsoft e quindi fare clic su **Crea una nuova app**.

2. Digitare un nome per l'app e fare clic su **Riserva nome dell'app**. Verrà creata una nuova registrazione a Windows Store per l'app.

3. In Visual Studio creare un nuovo progetto di app di Windows Store in Visual C# usando il modello **App vuota** universale di Windows e quindi fare clic su **OK**.

4. Accettare le impostazioni predefinite per le versioni minima e di destinazione della piattaforma.

5. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di app di Windows Store, scegliere **Store**, quindi fare clic su **Associa applicazione a Store**. Viene visualizzata la procedura guidata **Associa l'applicazione con Windows Store** .

6. Nella procedura guidata accedere con il proprio account Microsoft.

7. Fare clic sull'app registrata nel passaggio 2, fare clic su **Avanti** e quindi su **Associa**. Le informazioni di registrazione a Windows Store necessarie verranno aggiunte al manifesto dell'applicazione.

8. Nella pagina [Windows Dev Center](http://dev.windows.com/overview) della nuova app fare clic su **Servizi**, quindi su **Notifiche push** e infine su **WNS/MPNS**.

9. Fare clic su **Nuova notifica**.

10. Fare clic sul modello **Blank (Toast)** (Vuoto - Avviso popup) e quindi fare clic su **OK**.

11. Immettere un **nome** per la notifica e un messaggio **contesto** visivo. Fare clic su **Salva come bozza**.

12. Passare al [portale di registrazione delle applicazioni](http://apps.dev.microsoft.com) ed eseguire l'accesso.

13. Fare clic sul nome dell'applicazione. Prendere nota della password di **Segreto applicazione** e dell'**ID di sicurezza (SID) del pacchetto** presente nelle impostazioni della piattaforma **Windows Store**.

     > [AZURE.WARNING]
    Il segreto applicazione e il SID di pacchetto sono importanti credenziali di sicurezza. Non condividere questi valori con altri utenti né distribuirli con l'app.

## <a name="configure-your-notification-hub"></a>Configurare l'hub di notifica
[!INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Selezionare l'opzione <b>Servizi di notifica</b> e l'opzione <b>Windows (WNS)</b>. Immettere quindi la password di <b>Segreti applicazione</b> nel campo <b>Chiave di sicurezza</b>. Immettere il <b>valore di SID pacchetto</b> ottenuto da WNS nella sezione precedente e quindi fare clic su <b>Salva</b>.</p>
</li>
</ol>

&emsp;&emsp;![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

L'hub di notifica è ora configurato per l'uso con Servizi notifica Push Windows e si dispone delle stringhe di connessione per registrare l'app e inviare le notifiche.

## <a name="connect-your-app-to-the-notification-hub"></a>Connettere l'app all'hub di notifica
1. In Visual Studio fare clic con il pulsante destro del mouse sulla soluzione e quindi scegliere **Gestisci pacchetti NuGet**.
   
    Verrà visualizzata la finestra di dialogo **Gestisci pacchetti NuGet** .
2. Cercare `WindowsAzure.Messaging.Managed` , quindi fare clic su **Installa**e accettare le condizioni per l'utilizzo.
   
    ![][20]
   
    Verrà scaricato, installato e aggiunto un riferimento alla libreria di Messaggistica di Azure per Windows tramite il <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">pacchetto WindowsAzure.Messaging.Managed NuGet</a>.
3. Aprire il file di progetto App.xaml.cs e aggiungere le istruzioni `using` seguenti. 
   
        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;
4. In App.xaml.cs aggiungere inoltre alla classe **App** la definizione del metodo **InitNotificationsAsync** seguente:
   
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
   
            var hub = new NotificationHub("< your hub name>", "<Your DefaultListenSharedAccessSignature connection string>");
            var result = await hub.RegisterNativeAsync(channel.Uri);
   
            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
   
        }
   
    Questo codice recupera il valore di ChannelURI per l'app da Servizi notifica Push Windows e quindi lo registra con l'hub di notifica.
   
   > [!NOTE]
   > Sostituire il segnaposto "your hub name" con il nome dell'hub di notifica visualizzato nel portale di Azure. Sostituire anche il segnaposto relativo alla stringa di connessione con la stringa di connessione **DefaultListenSharedAccessSignature** ottenuta nella pagina **Criteri di accesso** dell'hub di notifica in una sezione precedente.
   > 
   > 
5. All'inizio del gestore dell'evento **OnLaunched** in App.xaml.cs, aggiungere la chiamata seguente al nuovo metodo **InitNotificationsAsync**:
   
        InitNotificationsAsync();
   
    In questo modo si garantisce che il valore di ChannelURI venga registrato nell'hub di notifica a ogni avvio dell'applicazione.
6. Premere **F5** per eseguire l'app. Verrà visualizzata una finestra di dialogo popup contenente la chiave di registrazione.

L'app è ora pronta per ricevere notifiche di tipo avviso popup.

## <a name="send-notifications"></a>Inviare notifiche
È possibile testare rapidamente la ricezione delle notifiche nell'app inviando le notifiche nel [portale di Azure](https://portal.azure.com/) con il pulsante **Invio di prova** dell'hub di notifica, come illustrato nella schermata seguente.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Le notifiche push vengono in genere inviate in un servizio back-end come Servizi mobili o ASP.NET usando una libreria compatibile. È anche possibile utilizzare l'API REST direttamente per inviare messaggi di notifica se una libreria non è disponibile per il back-end. 

In questa esercitazione verrà usata un'app semplice e verrà illustrato solo il test dell'app client mediante l'invio di notifiche tramite .NET SDK per gli hub di notifica in un'applicazione console, invece che in un servizio back-end. Come passaggio successivo per l'invio di notifiche da un back-end ASP.NET, è consigliabile vedere l'esercitazione [Uso di Hub di notifica di Azure per inviare notifiche agli utenti] . Si possono tuttavia usare gli approcci seguenti per l'invio di notifiche:

* **Interfaccia REST**: è possibile supportare la notifica su qualsiasi piattaforma back-end tramite l'[interfaccia REST](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).
* **Microsoft Azure Notification Hubs .NET SDK**: in Gestione pacchetti NuGet per Visual Studio eseguire [Install-Package Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).
* **Node.js** : vedere [Come usare Hub di notifica da Node.js](notification-hubs-nodejs-push-notification-tutorial.md).
* **App per dispositivi mobili di Azure**: per un esempio di come inviare notifiche da un'app per dispositivi mobili di Azure integrata con Hub di notifica, vedere [Aggiungere notifiche push per le app per dispositivi mobili](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).
* **Java/PHP**: per un esempio di invio di notifiche con le API REST, vedere "Come usare Hub di notifica da Java/PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

## <a name="optional-send-notifications-from-a-console-app"></a>(Facoltativo) Inviare notifiche da un'app console
Per inviare notifiche tramite un'applicazione console .NET seguire questa procedura. 

1. Fare clic con il pulsante destro del mouse sulla soluzione, scegliere **Aggiungi** e **Nuovo progetto**, quindi in **Visual C#** fare clic su **Windows**, su **Applicazione console** e infine su **OK**.
   
    Una nuova applicazione console Visual C# viene aggiunta alla soluzione. Questa operazione può essere eseguita anche in una soluzione separata.

2. In Visual Studio fare clic su **Strumenti**, selezionare **Gestione pacchetti NuGet** e quindi **Console di Gestione pacchetti**.
   
    In questo modo viene visualizzata la console di Gestione pacchetti in Visual Studio.
3. Nella finestra Console di Gestione pacchetti impostare **Progetto predefinito** sul nuovo progetto di applicazione console, quindi eseguire il comando seguente nella finestra della console:
   
        Install-Package Microsoft.Azure.NotificationHubs
   
    Verrà aggiunto un riferimento all’SDK dell’Hub di notifica di Azure mediante il <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacchetto NuGet Microsoft.Azure.NotificationHubs</a>.
   
    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)
4. Aprire il file Program.cs e aggiungere l'istruzione `using` seguente:
   
        using Microsoft.Azure.NotificationHubs;
5. Nella classe **Program** aggiungere il metodo seguente:
   
        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }
   
       Make sure to replace the "hub name" placeholder with the name of the notification hub that as it appears in the Azure Portal. Also, replace the connection string placeholder with the **DefaultFullSharedAccessSignature** connection string that you obtained from the **Access Policies** page of your Notification Hub in the section called "Configure your notification hub."
   
   > [!NOTE]
   > Assicurarsi di usare la stringa di connessione con accesso **Completo**, non **Attesa**. La stringa con accesso di tipo Attesa non dispone infatti delle autorizzazioni per l'invio di notifiche.
   > 
   > 
6. Aggiungere le righe seguenti nel metodo **Main** :
   
         SendNotificationAsync();
         Console.ReadLine();
7. Fare clic con il pulsante destro del mouse sul progetto di applicazione console in Visual Studio e fare clic su **Imposta come progetto di avvio** per impostarlo come progetto di avvio. Premere quindi **F5** per eseguire l'applicazione.
   
    Tutti i dispositivi registrati riceveranno una notifica di tipo avviso popup. Facendo clic o toccando il banner dell'avviso popup l'app viene caricata.

Per trovare tutti i payload supportati, è possibile consultare gli argomenti relativi al [catalogo degli avvisi popup], al [catalogo dei riquadri] e alla [panoramica delle notifiche] su MSDN.

## <a name="next-steps"></a>Passaggi successivi
In questo semplice esempio le notifiche di trasmissione sono state inviate a tutti i dispositivi Windows tramite il portale o un'app console. Come passaggio successivo, è consigliabile vedere l'esercitazione [Uso di Hub di notifica di Azure per inviare notifiche agli utenti] . Verrà illustrato come inviare notifiche da un back-end ASP.NET destinate a utenti specifici.

Se si desidera segmentare gli utenti per gruppi di interesse, vedere [Utilizzo di Hub di notifica per inviare le ultime notizie]. 

Per altre informazioni sull'uso di Hub di notifica, vedere [Panoramica dell'Hub di notifica](notification-hubs-push-notification-overview.md).

<!-- Images. -->
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->

[Uso di Hub di notifica di Azure per inviare notifiche agli utenti]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Utilizzo di Hub di notifica per inviare le ultime notizie]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md

[catalogo degli avvisi popup]: http://msdn.microsoft.com/library/windows/apps/hh761494.aspx
[catalogo dei riquadri]: http://msdn.microsoft.com/library/windows/apps/hh761491.aspx
[panoramica delle notifiche]: http://msdn.microsoft.com/library/windows/apps/hh779719.aspx

