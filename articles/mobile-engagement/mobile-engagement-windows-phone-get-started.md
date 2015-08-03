<properties
	pageTitle="Introduzione a Azure Mobile Engagement per app per Windows Phone Silverlight"
	description="Informazioni sull'uso di Azure Mobile Engagement con funzionalità di analisi e notifiche push per le app per Windows Phone Silverlight."
	services="mobile-engagement"
	documentationCenter="windows"
	authors="piyushjo"
	manager="dwrede"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="get-started-article" 
	ms.date="04/30/2015"
	ms.author="piyushjo" />

# Introduzione a Azure Mobile Engagement per app per Windows Phone Silverlight

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-dotnet-get-started.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-get-started.md)
- [iOS - Obj C](mobile-engagement-ios-get-started.md)
- [iOS - Swift](mobile-engagement-ios-swift-get-started.md)
- [Android](mobile-engagement-android-get-started.md)
- [Cordova](mobile-engagement-cordova-get-started.md)

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'utilizzo dell'app e inviare notifiche push a utenti segmentati di un'applicazione per Windows Phone Silverlight. Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Si creerà un'app per Windows Phone Silverlight vuota che raccoglie dati di base e riceve notifiche push tramite il Servizio notifica push Microsoft (MPNS). Al termine, si sarà in grado di trasmettere notifiche push a tutti i dispositivi o a utenti specifici in base alle proprietà dei loro dispositivi (tramite MPNS). Seguire anche l'esercitazione successiva per imparare a usare Mobile Engagement per rivolgersi a gruppi di dispositivi e a utenti specifici.

> [AZURE.NOTE]Se si usa Windows Phone 8.1 (non Silverlight) come piattaforma di destinazione, fare riferimento all'[esercitazione per le app di Windows universali](mobile-engagement-windows-store-dotnet-get-started.md).

Per completare questa esercitazione, è necessario disporre di:

+ Visual Studio 2013
+ [Mobile Engagement SDK per Windows Phone]

> [AZURE.IMPORTANT]Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni di Mobile Engagement per app per Windows Phone Silverlight e richiede un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

##<a id="setup-azme"></a>Configurare Mobile Engagement per l'app per Windows Phone Silverlight

1. Accedere al portale di gestione di Azure e quindi fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, quindi su **Mobile Engagement** e infine su **Crea**.

   	![][7]

3. Nella finestra popup che viene visualizzata, immettere le informazioni seguenti:

   	![][8]

	- **Nome dell'applicazione**: digitare il nome dell'applicazione. È possibile usare qualsiasi carattere.
	- **Piattaforma**: selezionare la piattaforma di destinazione (**Windows Phone Silverlight**) per l'app. Se l'app è destinata a più piattaforme, ripetere questa esercitazione per ogni piattaforma.
	- **Nome della risorsa dell'applicazione**: nome usato per rendere accessibile l'applicazione mediante API e URL. È necessario usare solo caratteri di URL convenzionali. Il nome generato automaticamente dovrebbe fornire una buona base di partenza. È anche necessario aggiungere il nome della piattaforma per evitare conflitti in quanto questo nome deve essere univoco.
	- **Percorso**: selezionare il data center in cui verrà ospitata l'app (e soprattutto la relativa raccolta).
	- **Raccolta**: se è già stata creata un'applicazione, selezionare una raccolta creata in precedenza, in caso contrario selezionare Nuova raccolta.
	- **Nome della raccolta**: nome che identifica il gruppo di applicazioni. Garantisce, inoltre, che tutte le app siano incluse in un gruppo per consentire calcoli aggregati delle metriche. Se applicabile, è possibile usare il nome della società o del reparto.

4. Selezionare l'app appena creata nella scheda **Applicazioni**.

5. Fare clic su **Informazioni di connessione** per visualizzare le impostazioni di connessione da inserire nell'integrazione dell'SDK nell'app per dispositivi mobili.

   	![][10]

6. Copiare la **stringa di connessione**, che è necessaria per identificare l'app nel codice dell'applicazione e connettersi a Mobile Engagement dall'app per il telefono.

   	![][11]

##<a id="connecting-app"></a>Connessione dell'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione relativa all'integrazione completa è disponibile nella [documentazione di Mobile Engagement SDK per Windows Phone].

Si creerà un'app di base con Visual Studio per illustrare l'integrazione.

###Creare un nuovo progetto Windows Phone Silverlight

1. Avviare Visual Studio e selezionare **Nuovo progetto** nella schermata principale.

2. Nella finestra popup selezionare `Store Apps` -> `Windows Phone Apps` -> `Blank App (Windows Phone Silverlight)`. Immettere i valori di `Name` e `Solution name` per l'app e fare clic su **OK**.

   	![][13]

3. È possibile scegliere la versione **Windows Phone 8.0** o **Windows Phone 8.1** come piattaforma di destinazione.

A questo punto è stata creata una nuova app per Windows Phone Silverlight in cui si integrerà Azure Mobile Engagement SDK.

###Connettere l'app al back-end di Mobile Engagement

1. Installare il pacchetto NuGet di [Mobile Engagement SDK per Windows Phone] nel progetto.

2. Aprire `WMAppManifest.xml` nella cartella Proprietà e assicurarsi che le funzionalità seguenti siano dichiarate (in caso contrario, aggiungerle) nel tag `<Capabilities />`:

		<Capability Name="ID_CAP_NETWORKING" />
		<Capability Name="ID_CAP_IDENTITY_DEVICE" />

	![][20]

3. Incollare la stringa di connessione copiata in precedenza per l'app Mobile Engagement e incollarla nel file `Resources\EngagementConfiguration.xml`, tra i tag `<connectionString>` e `</connectionString>`:

	![][21]

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

##<a id="monitor"></a>Abilitazione del monitoraggio in tempo reale

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Attività) al back-end di Mobile Engagement. A tale scopo, si creerà una sottoclasse di `MainPage` con la classe `EngagementPage` disponibile in Mobile Engagement SDK.

1. Aggiungere l'istruzione `using`:

		using Microsoft.Azure.Engagement;

2. Sostituire la superclasse di `MainPage`, in precedenza `PhoneApplicationPage`, con `EngagementPage`, come illustrato di seguito:

	![][22]

3. Nel file `MainPage.xml`: a. Aggiungere le dichiarazioni di spazi dei nomi:

			xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

	b. Sostituire `phone:PhoneApplicationPage` nel nome del tag XML con `engagement:EngagementPage`

###Controllare che l'app sia connessa tramite il monitoraggio in tempo reale

Questa sezione descrive come assicurarsi che l'app si connetta al back-end di Mobile Engagement usando la funzionalità di monitoraggio in tempo reale di Mobile Engagement.

1. Passare al portale di Mobile Engagement.

	Dal portale di Azure verificare che l'app usata per questo progetto sia attiva e fare clic sul pulsante di **Engagement** nella parte inferiore della schermata.

	![][26]

2. Verrà visualizzata la pagina delle impostazioni del portale di Engagement per l'app. Da questa posizione fare clic sulla scheda **Monitor**, come illustrato di seguito. ![][30]

3. Il monitoraggio è pronto per visualizzare qualsiasi dispositivo, in tempo reale, che avvierà l'applicazione.

4. Tornare a Visual Studio e avviare l'app nell'emulatore o in un dispositivo connesso.

5. A questo punto dovrebbe essere possibile visualizzare una sessione nel monitor. ![][33]

**Congratulazioni**. È stato completato il primo passaggio dell'esercitazione, con un'app che si connette al back-end di Mobile Engagement, che sta già inviando dati.

##<a id="integrate-push"></a>Abilitazione delle notifiche push e della messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

###Abilitare l'app alla ricezione di notifiche push MPNS

Aggiungere nuove funzionalità al file `WMAppManifest.xml`:

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

![][34]

###Inizializzare REACH SDK

1. In `App.xaml.cs` chiamare `EngagementReach.Instance.Init();` nella funzione `Application_Launching` subito dopo l'inizializzazione dell'agente:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. In `App.xaml.cs` chiamare `EngagementReach.Instance.OnActivated(e);` nella funzione `Application_Activated` subito dopo l'inizializzazione dell'agente:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

A questo punto, viene verificato che tutti i passaggi di questa integrazione di base siano stati effettuati correttamente.

##<a id="send"></a>Inviare una notifica all'app

A questo punto si creerà una campagna di notifica push semplice che invierà una notifica push all'app.

1. Passare alla scheda **REACH** nel portale di Mobile Engagement.

2. Fare clic su **Nuovo annuncio** per creare la campagna di push. ![][35]

3. Impostare il primo campo della campagna completando i passaggi seguenti: ![][36]

	1. Assegnare un nome alla campagna.
	2. Per **Tempo di recapito** selezionare *In qualsiasi momento* per consentire all'app di ricevere una notifica anche se non è stata avviata.
	3. Nel testo della notifica, digitare il titolo, che apparirà in grassetto nel push.
	4. Digitare quindi il messaggio.

4. Scorrere verso il basso e nella sezione relativa al contenuto selezionare **Solo notifica**. ![][37]

5. L'impostazione della campagna più semplice possibile è stata completata. Scorrere ancora verso il basso e fare clic su **Crea** per salvare la campagna.

6. Come ultimo passaggio, fare clic su **Attiva** per attivare la campagna e inviare notifiche push. ![][39]

7. Sul dispositivo apparirà una notifica di **completamento** della procedura: ![][40]

<!-- URLs. -->
[Mobile Engagement SDK per Windows Phone]: http://go.microsoft.com/?linkid=9874664
[documentazione di Mobile Engagement SDK per Windows Phone]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-phone-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-phone-get-started/create-azme-popup.png
[10]: ./media/mobile-engagement-windows-phone-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-phone-get-started/app-connection-info-page.png
[13]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[20]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[21]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[22]: ./media/mobile-engagement-windows-phone-get-started/subclassing.png
[26]: ./media/mobile-engagement-windows-phone-get-started/engage-button.png
[30]: ./media/mobile-engagement-windows-phone-get-started/clic-monitor-tab.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[35]: ./media/mobile-engagement-windows-phone-get-started/new-announcement.png
[36]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[37]: ./media/mobile-engagement-windows-phone-get-started/campaign-content.png
[39]: ./media/mobile-engagement-windows-phone-get-started/campaign-activate.png
[40]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png
 

<!---HONumber=July15_HO4-->