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
	ms.topic="article" 
	ms.date="04/30/2015" 
	ms.author="piyushjo" />
	
# Introduzione a Azure Mobile Engagement per app di Windows universali

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md) 
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md) 
- [iOS - Obj C](mobile-engagement-ios-get-started.md) 
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md) 

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'utilizzo dell'app e inviare notifiche push a utenti segmentati di un'applicazione di Windows universale. Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Si creerà un'app di Windows universale vuota che raccoglie dati di base relativi all'utilizzo dell'app e riceve notifiche push tramite Servizi notifica Push Windows (WNS). Al termine, si sarà in grado di trasmettere notifiche push a tutti i dispositivi o a utenti specifici in base alle proprietà dei loro dispositivi. Seguire anche l'esercitazione successiva per imparare a usare Mobile Engagement per rivolgersi a gruppi di dispositivi e a utenti specifici.

Per completare questa esercitazione, è necessario disporre di:

+ Visual Studio 2013
+ [Mobile Engagement SDK per app di Windows universali]

> [AZURE.IMPORTANT]Il completamento di questa esercitazione costituisce un prerequisito per tutte le altre esercitazioni di Mobile Engagement relative ad app di Windows universali. Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

##<a id="setup-azme"></a>Configurare Mobile Engagement per l'app di Windows universale

1. Accedere al portale di gestione di Azure e quindi fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, quindi su **Mobile Engagement** e infine su **Crea**.

   	![][7]

3. Nella finestra popup che viene visualizzata, immettere le informazioni seguenti:
 
   	![][8]

	- **Nome dell'applicazione**: digitare il nome dell'applicazione. È possibile usare qualsiasi carattere.
	- **Piattaforma**: selezionare la piattaforma di destinazione (**Universale di Windows**) per l'app. Se l'app è destinata a più piattaforme, ripetere questa esercitazione per ogni piattaforma. 
	- **Nome della risorsa dell'applicazione**: nome usato per rendere accessibile l'applicazione mediante API e URL. È necessario usare solo caratteri di URL convenzionali. Il nome generato automaticamente dovrebbe fornire una buona base di partenza. È anche necessario aggiungere il nome della piattaforma per evitare conflitti in quanto questo nome deve essere univoco.
	- **Percorso**: selezionare il data center in cui verrà ospitata l'app (e soprattutto la relativa raccolta).
	- **Raccolta**: se è già stata creata un'applicazione, selezionare una raccolta creata in precedenza, in caso contrario selezionare Nuova raccolta.
	- **Nome della raccolta**: nome che identifica il gruppo di applicazioni. Garantisce, inoltre, che tutte le app siano incluse in un gruppo per consentire calcoli aggregati delle metriche. Se applicabile, è possibile usare il nome della società o del reparto.

	> [AZURE.TIP]Se l'app universale è destinata a entrambe le piattaforme Windows e Windows Phone, è comunque preferibile creare due applicazioni Mobile Engagement, una per ogni piattaforma supportata. In questo modo si può definire la segmentazione corretta dei destinatari e inviare notifiche opportunamente mirate per ogni piattaforma.

4. Selezionare l'app appena creata nella scheda **Applicazioni**.

5. Fare clic su **Informazioni di connessione** per visualizzare le impostazioni di connessione da inserire nell'integrazione dell'SDK nell'app per dispositivi mobili.
 
   	![][10]

6. Copiare la **stringa di connessione**, che è necessaria per identificare l'app nel codice dell'applicazione e connettersi a Mobile Engagement dall'app universale.

   	![][11]

##<a id="connecting-app"></a>Connettere l'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione sull'integrazione completa è reperibile nella [documentazione di Mobile Engagement SDK per app di Windows universali].

Si creerà un'app di base con Visual Studio per illustrare l'integrazione.

###Creare un nuovo progetto di app di Windows universale

Se si ha già un'app e si ha familiarità con lo sviluppo di app di Windows universali, è possibile ignorare questo passaggio.

1. Avviare Visual Studio e selezionare **Nuovo progetto** nella schermata principale.

2. Nella finestra popup selezionare `Store Apps` -> `Universal Apps` -> `Blank App (Universal Apps)`. Immettere i valori di `Name` e `Solution name` per l'app e fare clic su **OK**.

   	![][13]

A questo punto è stata creata una nuova app di Windows universale in cui si integrerà Azure Mobile Engagement SDK.

###Connettere l'app al back-end di Mobile Engagement 

1. Installare il pacchetto NuGet di [Mobile Engagement SDK per app di Windows universali] nel progetto. Se l'app è destinata a entrambe le piattaforme Windows e Windows Phone, è necessario eseguire questa operazione per entrambi i progetti. Lo stesso pacchetto NuGet inserirà i file binari corretti per ogni piattaforma in ciascun progetto. 

2. Aprire `Package.appxmanifest` e aggiungere la stringa seguente, se non viene aggiunta automaticamente:
		
		Internet (Client)

	![][20]

3. Incollare la stringa di connessione copiata in precedenza per l'app Mobile Engagement e incollarla nel file `Resources\EngagementConfiguration.xml`, tra i tag `<connectionString>` e `</connectionString>`:

	![][22]

	>[AZURE.TIP]Se l'app è destinata a entrambe le piattaforme Windows e Windows Phone, è comunque preferibile creare due applicazioni Mobile Engagement, una per ogni piattaforma supportata. In questo modo si può definire la segmentazione corretta dei destinatari e inviare notifiche opportunamente mirate per ogni piattaforma.

4. Nel file `App.xaml.cs`:

	a. Aggiungere l'istruzione `using`:

			using Microsoft.Azure.Engagement;

	b. Inizializzare l'SDK nel metodo `OnLaunched`:
			
			protected override void OnLaunched(LaunchActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);
			
			  //... rest of the code
			}

	c. Inserire il codice seguente nel metodo `OnActivated` e aggiungere il metodo, se non è già presente:

			protected override void OnActivated(IActivatedEventArgs e)
			{
			  EngagementAgent.Instance.Init(e);
			
			  //... rest of the code
			}

##<a id="monitor"></a>Abilitazione del monitoraggio in tempo reale

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Activity) al back-end di Mobile Engagement. A tale scopo, si creerà una sottoclasse di `MainPage` con la classe `EngagementPage` disponibile in Mobile Engagement SDK.

1. 	Aggiungere l'istruzione `using`:

		using Microsoft.Azure.Engagement;

2. Sostituire la superclasse di `MainPage`, in precedenza `Page`, con `EngagementPage`:

	![][23]

3. Nel file `MainPage.xml`:

	a. Aggiungere le dichiarazioni di spazi dei nomi:

			xmlns:engagement="using:Microsoft.Azure.Engagement"
	
	b. Sostituire `Page` nel nome del tag XML con `engagement:EngagementPage`

###Controllare che l'app sia connessa tramite il monitoraggio in tempo reale

Questa sezione descrive come assicurarsi che l'app si connetta al back-end di Mobile Engagement usando la funzionalità di monitoraggio in tempo reale di Mobile Engagement.

1. Passare al portale di Mobile Engagement.

	Dal portale di Azure verificare che l'app usata per questo progetto sia attiva e fare clic sul pulsante di **Engagement** nella parte inferiore della schermata.

	![][26]

2. Verrà visualizzata la pagina delle impostazioni del portale di Engagement per l'app. Da questa posizione fare clic sulla scheda **Monitor**, come illustrato di seguito. ![][30]

3. Il monitoraggio è pronto per visualizzare qualsiasi dispositivo, in tempo reale, che avvierà l'applicazione.

4. Tornare a Visual Studio e avviare l'app nell'emulatore o in un dispositivo connesso.

5. A questo punto dovrebbe essere possibile visualizzare una sessione nel monitor in tempo reale. ![][33]

**Congratulazioni**. È stato completato il primo passaggio dell'esercitazione, con un'app che si connette al back-end di Mobile Engagement, che sta già inviando dati.

##<a id="integrate-push"></a>Abilitazione delle notifiche push e della messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

###Abilitare l'app alla ricezione di notifiche push WNS

1. Nel file `Package.appxmanifest`, nell'area `Notifications` della scheda `Application`, selezionare `Yes` per `Toast capable:`:

	![][35]

###Inizializzare REACH SDK

1. In `App.xaml.cs` chiamare `EngagementReach.Instance.Init();` nella funzione `OnLaunched` subito dopo l'inizializzazione dell'agente:

		protected override void OnLaunched(LaunchActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

2. In `App.xaml.cs` chiamare `EngagementReach.Instance.Init(e);` nella funzione `OnActivated` subito dopo l'inizializzazione dell'agente:

		protected override void OnActivated(IActivatedEventArgs e)
		{
		   EngagementAgent.Instance.Init(e);
		   EngagementReach.Instance.Init(e);
		}

A questo punto si è pronti per l'invio di un avviso popup e occorre verificare che l'integrazione di base sia stata completata.

###Concedere l'accesso a Mobile Engagement per inviare notifiche

1. Sarà necessario associare l'app a un'app di Windows Store per ottenere `Package security identifier (SID)` e `Secret Key` (segreto client). È possibile creare un'app da [Windows Store Dev Center] e quindi assicurarsi di **associare l'app a Store** da Visual Studio. 

2. Passare a **Impostazioni** nel portale di Mobile Engagement e fare clic sulla sezione `Native Push` a sinistra.

3. Fare clic sul pulsante `Edit` per immettere `Package security identifier (SID)` e `Secret Key` come illustrato di seguito:

	![][36]

##<a id="send"></a>Inviare una notifica all'app

A questo punto si creerà una campagna di notifica push semplice che invierà una notifica push all'app.

1. Passare alla scheda **REACH** nel portale di Mobile Engagement.

2. Fare clic su **Nuovo annuncio** per creare la campagna di push. ![][37]

3. Impostare il primo campo della campagna completando i passaggi seguenti: ![][38]

	a. Assegnare un nome alla campagna.

	b. Per **Tempo di recapito** selezionare *In qualsiasi momento* per consentire all'app di ricevere una notifica anche se non è stata avviata.

	c. Nel testo della notifica digitare il **titolo** che apparirà in grassetto nel push.

	d. Digitare quindi il messaggio.

4. Scorrere verso il basso e nella sezione relativa al contenuto selezionare **Solo notifica**. ![][39]

5. L'impostazione della campagna più semplice possibile è stata completata. Scorrere ancora verso il basso e fare clic su **Crea** per salvare la campagna.

6. Come ultimo passaggio, fare clic su **Attiva** per attivare la campagna e inviare notifiche push. ![][41]

Dovrebbe essere possibile visualizzare una notifica popup inviata dalla campagna sul dispositivo. Per visualizzare questa notifica, è necessario chiudere l'app. Se l'app era in esecuzione, per ricevere la notifica popup assicurarsi quindi di averla chiusa per alcuni minuti prima di attivare la campagna. Se si vuole integrare la notifica in-app in modo da visualizzare la notifica all'apertura, vedere [App di Windows universali - Integrazione di sovrimpressioni].

<!-- URLs. -->
[documentazione di Mobile Engagement SDK per app di Windows universali]: ../mobile-engagement-windows-store-integrate-engagement/
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

<!--HONumber=54--> 