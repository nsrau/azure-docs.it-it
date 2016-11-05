---
title: Introduzione ad Azure Mobile Engagement per app per Windows Phone Silverlight
description: Informazioni sull'uso di Azure Mobile Engagement con funzionalità di analisi e notifiche push per le app per Windows Phone Silverlight.
services: mobile-engagement
documentationcenter: windows
author: piyushjo
manager: dwrede
editor: ''

ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-phone
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 08/19/2016
ms.author: piyushjo

---
# <a name="get-started-with-azure-mobile-engagement-for-windows-phone-silverlight-apps"></a>Introduzione ad Azure Mobile Engagement per app per Windows Phone Silverlight
[!INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'uso dell'app e sull'invio di notifiche push a utenti segmentati di un'applicazione Silverlight per Windows Phone.
Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Si creerà un'app per Windows Phone Silverlight vuota che raccoglie dati di base e riceve notifiche push tramite il Servizio notifica push Microsoft (MPNS).

> [!NOTE]
> Se si usa Windows Phone 8.1 (non Silverlight) come piattaforma di destinazione, fare riferimento all' [esercitazione per le app di Windows universali](mobile-engagement-windows-store-dotnet-get-started.md).
> 
> 

Per completare questa esercitazione, è necessario disporre di:

* Visual Studio 2013
* [MicrosoftAzure.MobileEngagement] 

> [!NOTE]
> Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).
> 
> 

## <a name="<a-id="setup-azme"></a>setup-mobile-engagement-for-your-windows-phone-app"></a><a id="setup-azme"></a>Configurare Mobile Engagement per l'app per Windows Phone
[!INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

## <a name="<a-id="connecting-app"></a>connect-your-app-to-the-mobile-engagement-backend"></a><a id="connecting-app"></a>Connettere l'app al back-end di Mobile Engagement
Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione relativa all'integrazione completa è disponibile nella [Integrazione di Mobile Engagement SDK per Windows Phone](mobile-engagement-windows-phone-sdk-overview.md)

Si creerà un'app di base con Visual Studio per illustrare l'integrazione.

### <a name="create-a-new-windows-phone-silverlight-project"></a>Creare un nuovo progetto Windows Phone Silverlight
I passaggi seguenti presuppongono l'utilizzo di Visual Studio 2015 anche se i passaggi sono simili nelle versioni precedenti di Visual Studio. 

1. Avviare Visual Studio e selezionare **Nuovo progetto** nella schermata **Home**.
2. Nella finestra popup selezionare **Windows 8** -> **Windows Phone** -> **Applicazione vuota (Silverlight per Windows Phone)**. Inserire **Nome** e **Nome soluzione** dell'app, quindi fare clic su **OK**.
   
    ![][1]
3. È possibile scegliere la versione **Windows Phone 8.0** o **Windows Phone 8.1** come piattaforma di destinazione.

A questo punto è stata creata una nuova app per Windows Phone Silverlight in cui si integrerà Azure Mobile Engagement SDK.

### <a name="connect-your-app-to-the-mobile-engagement-backend"></a>Connettere l'app al back-end di Mobile Engagement
1. Installare il pacchetto nuget [MicrosoftAzure.MobileEngagement] nel progetto.
2. Aprire `WMAppManifest.xml` nella cartella Proprietà e assicurarsi che le funzionalità seguenti siano dichiarate (in caso contrario, aggiungerle) nel tag `<Capabilities />`:
   
        <Capability Name="ID_CAP_NETWORKING" />
        <Capability Name="ID_CAP_IDENTITY_DEVICE" />
   
    ![][2]
3. Incollare la stringa di connessione copiata in precedenza per l'app Mobile Engagement e incollarla nel file `Resources\EngagementConfiguration.xml`, tra i tag `<connectionString>` e `</connectionString>`:
   
    ![][3]
4. Nel file `App.xaml.cs`:
   
    a. Aggiungere l'istruzione `using`:
   
            using Microsoft.Azure.Engagement;
   
    b. Inizializzare l'SDK nel metodo `Application_Launching`:
   
            private void Application_Launching(object sender, LaunchingEventArgs e)
            {
              EngagementAgent.Instance.Init();
            }
   
    c. Inserire quanto segue in `Application_Activated`:
   
            private void Application_Activated(object sender, ActivatedEventArgs e)
            {
               EngagementAgent.Instance.OnActivated(e);
            }

## <a name="<a-id="monitor"></a>enable-real-time-monitoring"></a><a id="monitor"></a>Abilitare il monitoraggio in tempo reale
Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Activity) al back-end di Mobile Engagement.

1. Nel MainPage.xaml.cs, aggiungere l’istruzione `using`:
   
        using Microsoft.Azure.Engagement;
2. Sostituire la classe di base di **MainPage**, che si trova prima di **PhoneApplicationPage**, con **EngagementPage**.
   
        class MainPage : EngagementPage 
3. Nel file `MainPage.xml`:
   
    a. Aggiungere le dichiarazioni di spazi dei nomi:
   
         xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"
   
    b. Sostituire `phone:PhoneApplicationPage` nel nome del tag XML con `engagement:EngagementPage`

## <a name="<a-id="monitor"></a>connect-app-with-real-time-monitoring"></a><a id="monitor"></a>Connettere l'app con monitoraggio in tempo reale
[!INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

## <a name="<a-id="integrate-push"></a>enable-push-notifications-and-in-app-messaging"></a><a id="integrate-push"></a>Abilitare le notifiche push e la messaggistica in-app
Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement.
Le sezioni seguenti consentono di configurare l'app per la ricezione.

### <a name="enable-your-app-to-receive-mpns-push-notifications"></a>Abilitare l'app alla ricezione di notifiche push MPNS
Aggiungere nuove funzionalità al file `WMAppManifest.xml` :

        ID_CAP_PUSH_NOTIFICATION
        ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

### <a name="initialize-the-reach-sdk"></a>Inizializzare REACH SDK
1. In `App.xaml.cs` chiamare `EngagementReach.Instance.Init();` nella funzione **Application_Launching** subito dopo l'inizializzazione dell'agente:
   
        private void Application_Launching(object sender, LaunchingEventArgs e)
        {
           EngagementAgent.Instance.Init();
           EngagementReach.Instance.Init();
        }
2. In `App.xaml.cs` chiamare `EngagementReach.Instance.OnActivated(e);` nella funzione **Application_Activated** subito dopo l'inizializzazione dell'agente:
   
        private void Application_Activated(object sender, ActivatedEventArgs e)
        {
           EngagementAgent.Instance.OnActivated(e);
           EngagementReach.Instance.OnActivated(e);
        }

Le impostazioni sono state completate. Ora è necessario verificare di avere eseguito correttamente l'integrazione di base.

## <a name="<a-id="send"></a>send-a-notification-to-your-app"></a><a id="send"></a>Inviare una notifica all'app
[!INCLUDE [Create Windows Push campaign](../../includes/mobile-engagement-windows-push-campaign.md)]

Si dovrebbe ora vedere una notifica sul dispositivo che verrà visualizzata come una notifica all'interno dell'app se l'app è aperta in caso contrario come una notifica popup come quella seguente: 

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Documentazione di Mobile Engagement Windows Phone SDK]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png



<!--HONumber=Oct16_HO2-->


