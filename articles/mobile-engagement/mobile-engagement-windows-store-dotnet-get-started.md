<properties
	pageTitle="Introduzione a Azure Mobile Engagement per app di Windows universali"
	description="Informazioni sull'uso di Azure Mobile Engagement con funzionalità di analisi e notifiche push per le app di Windows universali."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="09/22/2015"
	ms.author="piyushjo" />

# Introduzione a Azure Mobile Engagement per app di Windows universali

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'utilizzo dell'app e inviare notifiche push a utenti segmentati di un'applicazione di Windows universale. Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Si creerà un'app di Windows universale vuota che raccoglie dati di base relativi all'utilizzo dell'app e riceve notifiche push tramite Servizi notifica Push Windows (WNS).

Per completare questa esercitazione, è necessario disporre di:

+ Visual Studio 2013
+ Pacchetto Nuget [MicrosoftAzure.MobileEngagement]

> [AZURE.IMPORTANT]Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Mobile Engagement relative ad app di Windows universali. Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-IT%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

##<a id="setup-azme"></a>Configurare Mobile Engagement per l'app di Windows universale

[AZURE.INCLUDE [Creare l'app Mobile Engagement nel portale](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Connettere l'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione sull'integrazione completa è reperibile nella [integrazione di Mobile Engagement SDK per app di Windows universali](../mobile-engagement-windows-store-sdk-overview/).

Si creerà un'app di base con Visual Studio per illustrare l'integrazione.

###Creare un nuovo progetto di app di Windows universale

I passaggi seguenti presuppongono l'utilizzo di Visual Studio 2015 anche se i passaggi sono simili nelle versioni precedenti di Visual Studio.

1. Avviare Visual Studio e selezionare **Nuovo progetto** nella schermata **Home**.

2. Nella finestra a comparsa, selezionare **Windows 8** -> **Universal** -> **applicazione vuota (Universal Windows 8.1)**. Inserire **Nome** e **Nome soluzione** dell’applicazione, quindi fare clic su **OK**.

    ![][1]

> [AZURE.IMPORTANT]L’Engagement per dispositivi mobili di Azure non supporta ancora le App Windows universali di Windows 10.

A questo punto è stata creata una nuova app di Windows universale in cui si integrerà Azure Mobile Engagement SDK.

###Connettere l'app al back-end di Mobile Engagement

1. Installare il pacchetto nuget [MicrosoftAzure.MobileEngagement] nel progetto. Se l'app è destinata a entrambe le piattaforme Windows e Windows Phone, è necessario eseguire questa operazione per entrambi i progetti. Lo stesso pacchetto NuGet inserisce i file binari corretti per ogni piattaforma in ciascun progetto.

2. Aprire **Package.appxmanifest** e assicurarsi che venga aggiunta la funzionalità seguente:

		Internet (Client)

	![][2]

3. Copiare la stringa di connessione copiata in precedenza per l'app Mobile Engagement e incollarla nel file `Resources\EngagementConfiguration.xml`, tra i tag `<connectionString>` e `</connectionString>`:

	![][3]

	>[AZURE.TIP]Se l'app è destinata a entrambe le piattaforme Windows e Windows Phone, è comunque preferibile creare due applicazioni Mobile Engagement, una per ogni piattaforma supportata. In questo modo si può definire la segmentazione corretta dei destinatari e inviare notifiche opportunamente mirate per ogni piattaforma.

4. Nel file `App.xaml.cs`:

	a. Aggiungere l'istruzione `using`:

			using Microsoft.Azure.Engagement;

	b. Inizializzare l’SDK nel metodo **OnLaunched**:

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

	c. Inserire il codice seguente nel metodo **OnActivated** e aggiungere il metodo, se non è già presente:

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);

			  //... rest of the code
			}

##<a id="monitor"></a>Abilitare il monitoraggio in tempo reale

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Activity) al back-end di Mobile Engagement.

1. 	Nel **MainPage.xaml.cs**, aggiungere l’istruzione `using`:

		using Microsoft.Azure.Engagement;

2. Sostituire la classe di base **MainPage** da **Page** a **EngagementPage**:

		class MainPage : EngagementPage

3. Nel file `MainPage.xaml`:

	a. Aggiungere le dichiarazioni di spazi dei nomi:

		xmlns:engagement="using:Microsoft.Azure.Engagement"

	b. Sostituire **Page** nel nome del tag XML con **engagement:EngagementPage**.
	
> [AZURE.IMPORTANT]Se la pagina esegue l'override del metodo `OnNavigatedTo`, accertarsi di chiamare `base.OnNavigatedTo(e)`. In caso contrario, l'attività non verrà segnalata (`EngagementPage` chiama `StartActivity` all'interno del relativo metodo `OnNavigatedTo`). Ciò è particolarmente importante in un progetto Windows Phone in cui il modello predefinito ha un metodo `OnNavigatedTo`.

##<a id="monitor"></a>Connettere l'app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connettere l'app con monitoraggio in tempo reale](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Abilitare le notifiche push e la messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

###Abilitare l'app alla ricezione di notifiche push WNS

1. Nel file `Package.appxmanifest`, nella scheda in **Applicazione**, in **Notifiche** selezionare **Sì** per **Popup supportati:**:

	![][5]

###Inizializzare REACH SDK

1. In `App.xaml.cs`, richiamare **EngagementReach.Instance.Init();** nella funzione **OnLaunched** subito dopo l'inizializzazione dell'agente:

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

2. In `App.xaml.cs`, richiamare **EngagementReach.Instance.Init();** nella funzione **OnActivated** subito dopo l'inizializzazione dell'agente:

		protected override void OnActivated(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

Ora la configurazione per l’invio di popup è pronta. Ora è necessario verificare di avere eseguito correttamente l'integrazione di base.

###Concedere l'accesso a Mobile Engagement per inviare notifiche

1. Sarà necessario associare l'app a un'app di Windows Store per ottenere **Identificatore di sicurezza del pacchetto (SID)** e ** Chiave privata** (segreto client). È possibile creare un'app da [Windows Store Dev Center] e quindi assicurarsi di **associare l'app a Store** da Visual Studio.

	![][7]

2. Passare a **Impostazioni** del portale Mobile Engagement e fare clic sulla sezione **Push nativo** a sinistra

3. Fare clic sul pulsante **Modifica** per immettere l'**Identificatore di sicurezza pacchetto (SID)** e la **Chiave segreta**, come illustrato di seguito:

	![][6]

##<a id="send"></a>Inviare una notifica all'app

[AZURE.INCLUDE [Creare una campagna Push Windows](../../includes/mobile-engagement-windows-push-campaign.md)]

Dovrebbe essere possibile visualizzare una notifica popup inviata dalla campagna sul dispositivo. Per visualizzare questa notifica, è necessario chiudere l'app. Se l'app era in esecuzione, per ricevere la notifica popup assicurarsi quindi di averla chiusa per alcuni minuti prima di attivare la campagna. Se si vuole integrare la notifica in-app in modo da visualizzare la notifica all'apertura, vedere [App di Windows universali - Integrazione di sovrimpressioni].

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Windows Store Dev Center]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
[App di Windows universali - Integrazione di sovrimpressioni]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png

<!---HONumber=Oct15_HO1-->