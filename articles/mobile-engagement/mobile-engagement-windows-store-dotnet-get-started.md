<properties
	pageTitle="Introduzione a Azure Mobile Engagement per app di Windows universali"
	description="Informazioni sull'uso di Azure Mobile Engagement con funzionalità di analisi e notifiche push per le app di Windows universali."
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="02/29/2016"
	ms.author="piyushjo" />

# Introduzione a Azure Mobile Engagement per app di Windows universali

> [AZURE.SELECTOR]
- [Windows universale](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'utilizzo dell'app e inviare notifiche push a utenti segmentati di un'applicazione di Windows universale. Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Si creerà un'app di Windows universale vuota che raccoglie dati di base relativi all'utilizzo dell'app e riceve notifiche push tramite Servizi notifica Push Windows (WNS).

Per completare questa esercitazione, è necessario disporre di:

+ Visual Studio 2013
+ Pacchetto Nuget [MicrosoftAzure.MobileEngagement]

> [AZURE.IMPORTANT] Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Mobile Engagement relative ad app di Windows universali. Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-IT%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

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

A questo punto è stata creata una nuova app di Windows universale in cui si integrerà Azure Mobile Engagement SDK.

###Connettere l'app al back-end di Mobile Engagement

1. Installare il pacchetto NuGet [MicrosoftAzure.MobileEngagement] nel progetto. Se l'app è destinata a entrambe le piattaforme Windows e Windows Phone, è necessario eseguire questa operazione per entrambi i progetti. Per Windows 8.x e Windows Phone 8.1 lo stesso pacchetto NuGet inserisce i file binari corretti specifici della piattaforma in ogni progetto.

2. Aprire **Package.appxmanifest** e assicurarsi che venga aggiunta la funzionalità seguente:

		Internet (Client)

	![][2]

3. Copiare la stringa di connessione copiata in precedenza per l'app Mobile Engagement e incollarla nel file `Resources\EngagementConfiguration.xml`, tra i tag `<connectionString>` e `</connectionString>`:

	![][3]

	>[AZURE.TIP] Se l'app è destinata a entrambe le piattaforme Windows e Windows Phone, è comunque preferibile creare due applicazioni Mobile Engagement, una per ogni piattaforma supportata. In questo modo si può definire la segmentazione corretta dei destinatari e inviare notifiche opportunamente mirate per ogni piattaforma.

4. Nel file `App.xaml.cs`:

	a. Aggiungere l'istruzione `using`:

			using Microsoft.Azure.Engagement;

	b. Aggiungere un metodo dedicato all'impostazione e inizializzazione di Engagement:

           private void InitEngagement(IActivatedEventArgs e)
           {
             EngagementAgent.Instance.Init(e);

			 //... rest of the code
           }

    c. Inizializzare l'SDK nel metodo **OnLaunched**:

			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  InitEngagement(e);

			  //... rest of the code
			}

	c. Inserire il codice seguente nel metodo **OnActivated** e aggiungere il metodo, se non è già presente:

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  InitEngagement(e);

			  //... rest of the code
			}

##<a id="monitor"></a>Abilitare il monitoraggio in tempo reale

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Activity) al back-end di Mobile Engagement.

1. 	Nel file **MainPage.xaml.cs** aggiungere la istruzione `using` seguente:

		using Microsoft.Azure.Engagement.Overlay;

2. Sostituire la classe di base **MainPage** da **Page** a **EngagementPageOverlay**:

		class MainPage : EngagementPageOverlay

3. Nel file `MainPage.xaml`:

	a. Aggiungere le dichiarazioni di spazi dei nomi:

		xmlns:engagement="using:Microsoft.Azure.Engagement.Overlay"

	b. Sostituire **Page** nel nome del tag XML con **engagement:EngagementPageOverlay**.
	
> [AZURE.IMPORTANT] Se la pagina esegue l'override del metodo `OnNavigatedTo`, accertarsi di chiamare `base.OnNavigatedTo(e)`. In caso contrario, l'attività non verrà segnalata (`EngagementPage` chiama `StartActivity` all'interno del relativo metodo `OnNavigatedTo`). Questo aspetto è particolarmente importante in un progetto Windows Phone in cui il modello predefinito ha un metodo `OnNavigatedTo`.

##<a id="monitor"></a>Connettere l'app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connettere l'app con monitoraggio in tempo reale](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Abilitare le notifiche push e la messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

###Abilitare l'app alla ricezione di notifiche push WNS

1. Nel file `Package.appxmanifest` scegliere la scheda **Applicazione** e impostare **Popup supportati** su **Sì** in **Notifiche**.

	![][5]

###Inizializzare REACH SDK

In `App.xaml.cs` chiamare **EngagementReach.Instance.Init(e);** nella funzione **InitEngagement** subito dopo l'inizializzazione dell'agente:

        private void InitEngagement(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

Ora la configurazione per l’invio di popup è pronta. Ora è necessario verificare di avere eseguito correttamente l'integrazione di base.

###Concedere l'accesso a Mobile Engagement per inviare notifiche

1. Aprire [Windows Dev Center per Windows Store] nel Web browser, accedere e creare un account, se necessario.
2. Fare clic su **Dashboard** nell'angolo superiore destro, quindi scegliere **Crea nuova applicazione** dal menu nel pannello sinistro. 

	![][9]

2. Creare l'app riservandone il nome.

	![][10]

3. Dopo la creazione dell'app, passare a **Servizi -> Notifiche push** dal menu a sinistra.

	![][11]

4. Nella sezione relativa alle notifiche push, fare clic sul collegamento relativo al sito **servizi Live**.

	![][12]

5. Verrà visualizzata la sezione relativa alle credenziali push. Assicurarsi di trovarsi nella sezione **Impostazioni app** e quindi copiare i valori di **SID pacchetto** e **Segreto client**.

	![][13]

6. Passare a **Impostazioni** del portale Mobile Engagement e fare clic sulla sezione **Push nativo** a sinistra. Fare quindi clic sul pulsante **Modifica** per immettere l'**Identificatore di sicurezza pacchetto (SID)** e la **Chiave segreta**, come illustrato di seguito:

	![][6]

8. Assicurarsi infine di avere associato l'app di Visual Studio all'app creata nell'App Store. Per eseguire questa operazione, fare clic su **Associa applicazione a Store** in Visual Studio.

	![][7]

##<a id="send"></a>Inviare una notifica all'app

[AZURE.INCLUDE [Creare una campagna Push Windows](../../includes/mobile-engagement-windows-push-campaign.md)]

Se l'app era in esecuzione verrà visualizzata una notifica all'interno dell'app, in caso contrario verrà visualizzato una notifica popup se l'applicazione è stata chiusa. Se appare una notifica all'interno dell'app, ma non una notifica popup, e si esegue l'applicazione in modalità di debug in Visual Studio, provare a selezionare **Eventi del ciclo di vita -> Sospendere** sulla barra degli strumenti per assicurarsi che l'applicazione venga effettivamente sospesa. Se è stato appena fatto clic sul pulsante Home durante il debug dell'applicazione in Visual Studio, allora non sempre viene sospeso e durante la notifica verrà visualizzato come in-app, non verrà visualizzato come notifica popup.

![][8]

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9864592
[Windows Dev Center per Windows Store]: https://dev.windows.com
[Windows Universal Apps - Overlay integration]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-store-dotnet-get-started/universal-app-creation.png
[2]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[3]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[5]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[6]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/associate-app-store.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/vs-suspend.png
[9]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_create_app.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_app_name.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push.png
[12]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_1.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/dashboard_services_push_creds.png

<!---HONumber=AcomDC_0302_2016-->