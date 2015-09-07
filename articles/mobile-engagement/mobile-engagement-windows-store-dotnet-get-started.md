<properties
	pageTitle="Introduzione a Azure Mobile Engagement per app di Windows universali"
	description="Informazioni sull'uso di Azure Mobile Engagement con funzionalità di analisi e notifiche push per le app di Windows universali."
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-store"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="04/30/2015"
	ms.author="piyushjo"/>

# Introduzione a Azure Mobile Engagement per app di Windows universali

> [AZURE.SELECTOR]
- [Universal Windows](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS | Obj C](mobile-engagement-ios-get-started.md)
- [iOS | Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'utilizzo dell'app e inviare notifiche push a utenti segmentati di un'applicazione di Windows universale. Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Si creerà un'app di Windows universale vuota che raccoglie dati di base relativi all'utilizzo dell'app e riceve notifiche push tramite Servizi notifica Push Windows (WNS). Al termine dell'esercitazione, si sarà in grado di trasmettere notifiche push a tutti i dispositivi o a utenti specifici in base alle proprietà dei dispositivi. Seguire anche l'esercitazione successiva per imparare a usare Mobile Engagement per rivolgersi a gruppi di dispositivi e a utenti specifici.

Per completare questa esercitazione, è necessario disporre di:

+ Visual Studio 2013
+ [Mobile Engagement SDK per app di Windows universali]

> [AZURE.IMPORTANT]Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Mobile Engagement relative ad app di Windows universali. Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-IT%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

##<a id="setup-azme"></a>Configurare Mobile Engagement per l'app universale di Windows

1. Accedere al portale di Azure e quindi fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, quindi su **Mobile Engagement** e infine su **Crea**.

  	![][7]

3. Nella finestra popup che viene visualizzata, immettere le informazioni seguenti:

    ![][8]

	 - **Nome dell'applicazione**: digitare il nome dell'applicazione. È possibile usare qualsiasi carattere.
	 - **Piattaforma**: selezionare la piattaforma di destinazione (**Windows Universal**) per l'app. Se l'app è destinata a più piattaforme, ripetere questa esercitazione per ogni piattaforma.
	 - **Nome della risorsa dell'applicazione**: nome usato per rendere accessibile l'applicazione mediante API e URL. È necessario usare solo caratteri di URL convenzionali. Il nome generato automaticamente dovrebbe fornire una buona base di partenza. È anche necessario aggiungere il nome della piattaforma per evitare conflitti in quanto questo nome deve essere univoco.
	 - **Percorso**: selezionare il data center in cui verrà ospitata l'app (e soprattutto la relativa raccolta).
	 - **Raccolta**: se è già stata creata un'applicazione, selezionare una raccolta creata in precedenza, in caso contrario selezionare **Nuova raccolta**.
	 - **Nome della raccolta**: nome che identifica il gruppo di applicazioni. Garantisce, inoltre, che tutte le app siano incluse in un gruppo per consentire calcoli aggregati delle metriche. Se applicabile, è possibile usare il nome della società o del reparto.

	> [AZURE.TIP]Se l'app universale è destinata a entrambe le piattaforme Windows e Windows Phone, è comunque preferibile creare due applicazioni Mobile Engagement, una per ogni piattaforma supportata. In questo modo si può definire la segmentazione corretta dei destinatari e inviare notifiche opportunamente mirate per ogni piattaforma.

4. Selezionare l'app appena creata nella scheda **Applicazioni**.

5. Fare clic su **Informazioni di connessione** per visualizzare le impostazioni di **connessione** da inserire nell'integrazione dell'SDK nell'app per dispositivi mobili.

    ![][10]

6. Copiare la **stringa di connessione**, che è necessaria per identificare l'app nel codice dell'applicazione e connettersi a Mobile Engagement dall'app universale.

    ![][11]

##<a id="connecting-app"></a>Connettere l'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione sull'integrazione completa è reperibile nella documentazione di Mobile Engagement SDK per app di Windows universali.

Si creerà un'app di base con Visual Studio per illustrare l'integrazione.

###Creare un nuovo progetto di app di Windows universale

Se si ha già un'app e si ha familiarità con lo sviluppo di app di Windows universali, è possibile ignorare questo passaggio.

1. Avviare Visual Studio e selezionare **Nuovo progetto** nella schermata **Home**.

2. Nella finestra a comparsa, selezionare **Applicazioni Windows Store** -> **Applicazioni universali** -> **Applicazione vuota (applicazioni universali)**. Inserire **Nome** e **Nome soluzione** dell’applicazione, quindi fare clic su **OK**.

    ![][13]

A questo punto è stata creata una nuova app di Windows universale in cui si integrerà Azure Mobile Engagement SDK.

###Connettere l'app al back-end di Mobile Engagement

1. Installare il pacchetto NuGet di [Mobile Engagement SDK per app di Windows universali] nel progetto. Se l'app è destinata a entrambe le piattaforme Windows e Windows Phone, è necessario eseguire questa operazione per entrambi i progetti. Lo stesso pacchetto NuGet inserisce i file binari corretti per ogni piattaforma in ciascun progetto.

2. Aprire **Package.appxmanifest** e aggiungere quanto indicato di seguito se non viene aggiunto automaticamente:

		Internet (Client)

	![][20]

3. Copiare la stringa di connessione copiata in precedenza per l'app Mobile Engagement e incollarla nel file `Resources\EngagementConfiguration.xml`, tra i tag `<connectionString>` e `</connectionString>`:

	![][22]

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

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Activity) al back-end di Mobile Engagement. A tale scopo, si creerà una sottoclasse di **MainPage** con la classe **EngagementPage** disponibile in Mobile Engagement SDK.

1. 	Aggiungere l'istruzione `using`:

		   using Microsoft.Azure.Engagement;

2. Sostituire la classe con privilegi avanzata di **MainPage**, che si trova prima di **Page**, con **EngagementPage**:

	![][23]

3. Nel file `MainPage.xml`:

	a. Aggiungere le dichiarazioni di spazi dei nomi:

			xmlns:engagement="using:Microsoft.Azure.Engagement"

	b. Sostituire **Page** nel nome del tag XML con **engagement:EngagementPage**.
	
> [AZURE.IMPORTANT]Se la pagina esegue l'override del metodo `OnNavigatedTo`, accertarsi di chiamare `base.OnNavigatedTo(e)`. In caso contrario, l'attività non verrà segnalata (`EngagementPage` chiama `StartActivity` all'interno del relativo metodo `OnNavigatedTo`). Ciò è particolarmente importante in un progetto Windows Phone in cui il modello predefinito ha un metodo `OnNavigatedTo`.

###Controllare che l'app sia connessa tramite il monitoraggio in tempo reale

Questa sezione descrive come assicurarsi che l'app si connetta al back-end di Mobile Engagement usando la funzionalità di monitoraggio in tempo reale di Mobile Engagement.

1. Passare al portale di Mobile Engagement.

	Dal portale di Azure verificare che l'app usata per questo progetto sia aperta e fare clic sul pulsante **Attiva** nella parte inferiore della schermata.

	![][26]

2. Verrà visualizzata la pagina **Impostazioni** del portale di Engagement per l'app. Da questa posizione fare clic sulla scheda **Monitora**, come illustrato di seguito. ![][30]

3. Verranno visualizzati, in tempo reale, tutti i dispositivi in cui l'app verrà avviata.

4. Tornare a Visual Studio e avviare l'app nell'emulatore o in un dispositivo connesso.

5. A questo punto dovrebbe essere possibile visualizzare una sessione nel monitor in tempo reale. ![][33]

**Congratulazioni**. È stato completato il primo passaggio dell'esercitazione, con un'app che si connette al back-end di Mobile Engagement, che sta già inviando dati.

##<a id="integrate-push"></a>Abilitare le notifiche push e la messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

###Abilitare l'app alla ricezione di notifiche push WNS

1. Nel file `Package.appxmanifest`, nella scheda in **Applicazione**, in **Notifiche** selezionare **Sì** per **Popup supportati:**:

	![][35]

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

2. Passare a **Impostazioni** del portale Mobile Engagement e fare clic sulla sezione **Push nativo** a sinistra

3. Fare clic sul pulsante **Modifica** per immettere l'**Identificatore di sicurezza pacchetto (SID)** e la **Chiave segreta**, come illustrato di seguito:

	![][36]

##<a id="send"></a>Inviare una notifica all'app

A questo punto si crea una campagna di notifica push semplice che invia una notifica push all'app.

1. Passare alla scheda **REACH** nel portale di Mobile Engagement.

2. Fare clic su **Nuovo annuncio** per creare una campagna di notifica push. ![][37]

3. Impostare il primo campo della campagna completando i passaggi seguenti: ![][38]

	a. Assegnare un nome alla campagna.

	b. Per **Tempo di recapito** selezionare *In qualsiasi momento* per consentire all'app di ricevere una notifica anche se non è stata avviata.

	c. Nel testo della notifica digitare il **titolo** che apparirà in grassetto nel push.

	d. Digitare quindi il messaggio.

4. Scorrere verso il basso e nella sezione **Contenuto** selezionare **Solo notifica**. ![][39]

5. L'impostazione della campagna più semplice possibile è stata completata. Ora scorrere nuovamente verso il basso e fare clic sul pulsante **Crea** per salvare la campagna.

6. Come ultimo passaggio, fare clic su **Attiva** per attivare la campagna e inviare le notifiche push. ![][41]

Dovrebbe essere possibile visualizzare una notifica popup inviata dalla campagna sul dispositivo. Per visualizzare questa notifica, è necessario chiudere l'app. Se l'app era in esecuzione, per ricevere la notifica popup assicurarsi quindi di averla chiusa per alcuni minuti prima di attivare la campagna. Se si vuole integrare la notifica in-app in modo da visualizzare la notifica all'apertura, vedere [App di Windows universali - Integrazione di sovrimpressioni].

<!-- URLs. -->
[Mobile Engagement Windows Universal SDK documentation]: ../mobile-engagement-windows-store-integrate-engagement/
[Mobile Engagement SDK per app di Windows universali]: http://go.microsoft.com/?linkid=9864592
[Windows Store Dev Center]: http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409
[App di Windows universali - Integrazione di sovrimpressioni]: ../mobile-engagement-windows-store-integrate-engagement-reach/#overlay-integration

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-dotnet-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-dotnet-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-store-dotnet-get-started/UniversalAppCreation.png
[20]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-dotnet-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-dotnet-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-dotnet-get-started/engage-button.png
[27]: ./media/mobile-engagement-common/engagement-portal.png
[30]: ./media/mobile-engagement-windows-store-dotnet-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-store-dotnet-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-dotnet-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-dotnet-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-dotnet-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-content.png
[41]: ./media/mobile-engagement-windows-store-dotnet-get-started/campaign-activate.png

<!---HONumber=August15_HO9-->