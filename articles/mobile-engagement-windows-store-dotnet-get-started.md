<properties 
	pageTitle="Introduzione a Azure Mobile Engagement" 
	description="Come utilizzare Azure Mobile Engagement con Analisi e Notifiche push."
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="kpiteira" 
	manager="dwrede" 
	editor="" />

<tags 
	ms.service="mobile-engagement" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/11/2015" 
	ms.author="kapiteir" />
	
# Introduzione a Mobile Engagement

<div class="dev-center-tutorial-selector sublanding"><a href="/documentation/articles/mobile-engagement-windows-store-dotnet-get-started/" title="Windows Store" class="current">Windows Store</a><a href="/documentation/articles/mobile-engagement-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/documentation/articles/mobile-engagement-ios-get-started/" title="iOS">iOS</a><a href="/documentation/articles/mobile-engagement-android-get-started/" title="Android">Android</a></div>

In questo argomento viene illustrato come utilizzare Azure Mobile Engagement per comprendere l'utilizzo dell'app e inviare notifiche push a utenti segmentati di un'applicazione Windows Phone. 
Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Viene creata un'app Windows Store vuota che raccoglie dati di base e riceve notifiche push tramite il Servizio di notifica Push di Microsoft (MPNS). Al termine, sarà possibile trasmettere le notifiche push a tutti i dispositivi o a utenti specifici di destinazione in base alle proprietà dei dispositivi. Seguire anche l'esercitazione successiva per imparare a usare Mobile Engagement per indirizzarsi a specifici utenti e gruppi di dispositivi.


Per completare questa esercitazione, è necessario soddisfare i seguenti requisiti:

+ Visual Studio 2013
+ [Mobile Engagement Windows Store SDK]

> [AZURE.IMPORTANT] Il completamento di questa esercitazione è un prerequisito per tutte le altre esercitazioni di Mobile Engagement per le app Windows Store e richiede un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fit-it%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">versione di valutazione gratuita di Azure</a>.

##<a id="setup-azme"></a>Impostare Mobile Engagement per l'app

1. Accedere al portale di gestione di Azure e fare clic su **+NUOVO** nella parte inferiore della schermata.

2. Fare clic su **Servizi app**, quindi su **Mobile Engagement** e infine su **Crea**.

 	![][7]

3. Nella finestra popup che viene visualizzata, immettere le informazioni seguenti:
 
 	![][8]

	1. **Nome dell'applicazione**: è possibile digitare il nome dell'applicazione. È possibile utilizzare qualsiasi carattere.
	2. **Piattaforma**: selezionare la piattaforma di destinazione per l'app (se destinata a più piattaforme, ripetere questa esercitazione per ogni piattaforma).
	3. **Nome risorsa applicazione**: il nome con cui l'applicazione sarà accessibile tramite le API e gli URL. Si consiglia di utilizzare solo caratteri URL tradizionali: il nome generato automaticamente deve fornire una base sicura. Si consiglia inoltre di aggiungere il nome della piattaforma per evitare eventuali conflitti tra nomi poiché questo nome deve essere univoco
	4. **Posizione**: Selezionare il data center dove l'applicazione (e soprattutto la relativa raccolta - vedere di seguito) verrà ospitata.
	5. **Raccolta**: se è già stata creata un'applicazione, selezionare una raccolta creata in precedenza, in caso contrario selezionare Nuova raccolta.
	6. **Nome della raccolta**: rappresenta il gruppo di applicazioni. Garantisce, inoltre, che tutte le app si trovino in un gruppo che consentirà calcoli aggregati. È consigliabile utilizzare il nome della società o del reparto.


	Al termine, fare clic sul pulsante di controllo per completare la creazione dell'app.

4. A questo punto fare clic/selezionare l'app appena creata nella scheda **Applicazione**.
 
 	![][9]

5. Quindi fare clic su **Informazioni di connessione** per visualizzare le impostazioni di connessione da inserire nell'integrazione SDK.
 
 	![][10]

6. Infine, annotare la **Stringa di connessione**, che è l'elemento necessario per identificare l'app dal codice dell'applicazione.

 	![][11]

	>[AZURE.TIP] È possibile utilizzare l'icona "copia" sulla destra della stringa di connessione per copiarla negli Appunti per maggiore praticità.

##<a id="connecting-app"></a>Connessione dell'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", ossia l'integrazione minima richiesta per raccogliere dati e inviare una notifica push. La documentazione sull'integrazione completa è reperibile nella [documentazione dell'SDK Windows Store per Mobile Engagement].

Verrà creata un'app di base con Visual Studio per illustrare l'integrazione.

###Creare un nuovo progetto Windows Store

È possibile ignorare questo passaggio se già si dispone di un'app e si ha familiarità con lo sviluppo di Windows Store.

1. Avviare Visual Studio e selezionare **Nuovo progetto...** nella schermata principale.

 	![][12]

2. Nella finestra popup, (1) selezionare `Windows Store`, quindi (2) selezionare `applicazione vuota (XAML)`. Quindi, (3) inserire il  `Nome` e (4) il  `Nome soluzione` e fare clic su **OK**.

 	![][13]

A questo punto il progetto è stato creato con l'app demo in cui deve essere integrato Mobile Enegagement.

###Includere la libreria SDK nel progetto

Scaricare e integrare la libreria SDK

1. Scaricare l'[SDK Windows Store per Mobile Engagement]
2. Estrarre il file con estensione zip in una cartella nel computer.
3. Andare a  `PROJECT` e selezionare 'Gestisci pacchetti NuGet...'
4. Nella finestra popup, fare clic su Impostazioni
5. Fare clic sul pulsante '+' per creare una nuova origine

	![][17]

6. Fare clic sul  `...` pulsante nella parte inferiore per selezionare l'origine e passare alla cartella lib del downoad SDK estratto (vedere il passaggio 2), quindi scegliere `Seleziona`

	![][18]

7. L'SDK verrà aggiunto come origine. Fare clic su  `OK`. Selezionare il pacchetto nell'elenco di pacchetto "online", quindi fare clic su Installa

	![][19]


###Connettere l'app al back-end di Mobile Engagement con la stringa di connessione

1. Aprire  `Package.appxmanifest` e assicurarsi che contenga le dichiarazioni seguenti (aggiungerle se assenti)):
		
		Internet (Client)

	![][20]

2. Nello stesso file, aprire la scheda  `Dichiarazioni`:
	1. In  `Dichiarazioni disponibili`, aggiungere `Associazioni tipo file`
	2. Quindi nella parte delle  `Proprietà` a destra impostare il  `Nome` su  `engagement_log_file`
	3. Ancora nella parte delle  `Proprietà` a destra impostare il  `Tipo di file` su  `.set`
	4. Infine in  `Dichiarazioni disponibili`, aggiungere  `Aggiornamento file nella cache`

		![][21]
 
3. Tornare al portale Azure nella pagina **Informazioni di connessione** dell'app e copiare la **Stringa di connessione**.

	![][11]

4. Incollarla nel file  `Resources\EngagementConfiguration.xml` tra i tag `<connectionString>` e `</connectionString>` :

	![][22]

5. Nel file  `App.xaml.cs`:
	1. Aggiungere l'istruzione  `using`

			using Microsoft.Azure.Engagement;

	2. Inizializzare l'SDK nel metodo  `OnLaunched`:
			
			protected override void OnLaunched(LaunchActivatedEventArgs args)
			{
			 EngagementAgent.Instance.Init(args);
			
			 //... rest of the code
			}

	3. Inserire la seguente operazione in  `Application_Activated` (sarà necessario aggiungere questa operazione se non è già presente)

			protected override void OnActivated(IActivatedEventArgs args)
			{
			 EngagementAgent.Instance.Init(args);
			
			 //... rest of the code
			}

###Inviare una schermata a Mobile Engagement

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Attività) al back-end di Mobile Engagement. A tale scopo vengono create sottoclassi di  `MainPage` con  `EngagementPage` fornita dall'SDK Mobile Engagement.

1. A tale scopo, sostituire la superclasse  `MainPage`, che si trova prima di  `Page`, con EngagementPage, come illustrato di seguito:

	![][23]

	>[AZURE.NOTE] Non dimenticare di risolvere la classe se appare sottolineata in rosso aggiungendo  `using Microsoft.Azure.Engagement;` alle clausole `using`.

2. Nel file  `MainPage.xaml`:
	1. Aggiungere le dichiarazioni di spazi dei nomi:

			xmlns:engagement="using:Microsoft.Azure.Engagement"
	2. Sostituire  `Page` nel nome tag xml con  `engagement:EngagementPage`

##<a id="monitor"></a>Come verificare che l'app è connessa al monitoraggio in tempo reale

In questa sezione viene illustrato come assicurarsi che l'app si connetta al back-end di Mobile Engagement utilizzando la funzionalità di monitoraggio in tempo reale di Mobile Engagement.

1. Passare al portale Mobile Engagement.

	Dal portale Azure, verificare che sia attiva l'app utilizzata per questo progetto e fare clic sul pulsante **Attiva** nella parte inferiore.

	![][26]

2. Viene visualizzata la pagina delle impostazioni del portale Engagement per l'app. Da qui, fare clic sulla scheda **Monitoraggio**, come illustrato di seguito.
![][30]

3. Il monitoraggio è pronto per visualizzare qualsiasi dispositivo, in tempo reale, che avvierà l'applicazione.
![][31]

4. In Visual Studio, avviare l'app nell'emulatore o in un dispositivo connesso facendo clic sul triangolo verde e quindi selezionando il dispositivo.

5. A questo punto dovrebbe essere possibile visualizzare una sessione nel monitoraggio. 
![][33]

**Complimenti.** È stato completato il primo passaggio di questa esercitazione con un'applicazione che si connette al back-end di Mobile Engagement, che sta già inviando dati.


##<a id="integrate-push"></a>Abilitazione di notifiche push

Mobile Engagement consente di interagire con gli utenti e raggiungerli con le notifiche push e la messaggistica in-app nel contesto delle campagne. Questo modulo viene chiamato COPERTURA nel portale Mobile Engagement.
Nelle sezioni seguenti si imposterà l'app per la ricezione.

###Abilitare l'app alla ricezione di notifiche push MPNS

1. Aggiungere una nuova  `Associazione tipi file` al file  `Package.appxmanifest` nel pannello Dichiarazioni:

		Name: engagement_reach_content
		File type: .txt

	![][34]

2. Ancora nel file  `Package.appxmanifest`, nella scheda  `Application` in  `Notifiche`, selezionare `Sì` per `Popup supportati:`:

	![][35]

###Inizializzare l'SDK COPERTURA

1. In  `App.xaml.cs`, chiamare  `EngagementReach.Instance.Init(args);`  nella funzione  `OnLaunched` subito dopo l'inizializzazione dell'agente:

		protected override void OnLaunched(LaunchActivatedEventArgs args)
		{
		 EngagementAgent.Instance.Init(args);
		 EngagementReach.Instance.Init(args);
		}

2. In  `App.xaml.cs`, chiamare  `EngagementReach.Instance.OnActivated(args);`  nella funzione  `Application_Activated` subito dopo l'inizializzazione dell'agente:

		protected override void OnActivated(IActivatedEventArgs args)
		{
		 EngagementAgent.Instance.OnActivated(args);
		 EngagementReach.Instance.OnActivated(args);
		}

3. Eseguire l'applicazione. 
A questo punto si pronti per l'invio di un avviso popup e occorre verificare che l'integrazione di base sia stata effettuata correttamente.

###Concedere l'accesso a Mobile Engagement per inviare notifiche

1. Sarà quindi necessario associare l'app Windows Store per ottenere  `Package security identifier (SID)` e la  `chiave segreta` (*Segreto client*).

2. Quindi passare a Impostazioni del portale Mobile Engagement e fare clic sulla sezione  `Push nativo` a sinistra

3. Fare clic sul pulsante  `Modifica` per immettere l' `Identificatore di sicurezza pacchetto (SID)` e la  `Chiave segreta`, come illustrato di seguito:

	![][36]

##<a id="send"></a>Come inviare una notifica all'app

A questo punto verrà creata una campagna di notifica push semplice che invierà una notifica push all'app.

1. Passare alla scheda **COPERTURA** del portale Mobile Engagement.
2. Fare clic su **Nuovo annuncio** per creare una campagna di push.
![][37]

3. Impostare il primo campo della campagna completando i passaggi seguenti:
![][38]

	1. Assegnare un nome alla campagna.
	2. Per **Tempo di recapito** selezionare  *In qualsiasi momento* per consentire all'app di ricevere una notifica anche se non è stata avviata.
	3. Nel testo della notifica, digitare il titolo, che apparirà in grassetto nel push.
	4. Digitare quindi il messaggio.

4. Scorrere verso il basso e, nella sezione del contenuto, selezionare **Solo notifica**.
![][39]

5. L'impostazione della campagna di base è terminata. A questo punto, scorrere di nuovo verso il basso e creare la propria campagna per salvarla.
![][40]

6. Infine, attivare la campagna.
![][41]

Sul dispositivo apparirà una notifica, **Complimenti.**:



<!-- URLs. -->
[Documentazione dell'SDK Windows Store per Mobile Engagement]: ../mobile-engagement-windows-store-integrate-engagement/
[Mobile Engagement Windows Store SDK]: http://go.microsoft.com/?linkid=9864592

<!-- Images. -->
[7]: ./media/mobile-engagement-windows-store-get-started/create-mobile-engagement-app.png
[8]: ./media/mobile-engagement-windows-store-get-started/aux-create-app.png
[9]: ./media/mobile-engagement-windows-store-get-started/aux-select-app.png
[10]: ./media/mobile-engagement-windows-store-get-started/app-main-page-select-connection-info.png
[11]: ./media/mobile-engagement-windows-store-get-started/app-connection-info-page.png
[12]: ./media/mobile-engagement-windows-phone-get-started/new-project.png
[13]: ./media/mobile-engagement-windows-store-get-started/project-properties.png
[17]: ./media/mobile-engagement-windows-phone-get-started/manage-nuget-settings-new-source.png
[18]: ./media/mobile-engagement-windows-store-get-started/manage-nuget-settings-new-source-add-lib.png
[19]: ./media/mobile-engagement-windows-store-get-started/manage-nuget-settings-new-source-added.png
[20]: ./media/mobile-engagement-windows-store-get-started/manifest-capabilities.png
[21]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations.png
[22]: ./media/mobile-engagement-windows-store-get-started/add-connection-info.png
[23]: ./media/mobile-engagement-windows-store-get-started/subclass-page.png
[26]: ./media/mobile-engagement-windows-store-get-started/engage-button.png
[27]: ./media/mobile-engagement-windows-store-get-started/engagement-portal.png

[28]: ./media/mobile-engagement-windows-store-get-started/native-push-settings.png
[29]: ./media/mobile-engagement-windows-store-get-started/api-key.png
[30]: ./media/mobile-engagement-windows-store-get-started/clic-monitor-tab.png
[31]: ./media/mobile-engagement-windows-store-get-started/monitor.png
[32]: ./media/mobile-engagement-windows-store-get-started/launch.png
[33]: ./media/mobile-engagement-windows-phone-get-started/monitor.png
[34]: ./media/mobile-engagement-windows-store-get-started/manifest-declarations-reach.png
[35]: ./media/mobile-engagement-windows-store-get-started/manifest-toast.png
[36]: ./media/mobile-engagement-windows-store-get-started/enter-credentials.png
[37]: ./media/mobile-engagement-windows-store-get-started/new-announcement.png
[38]: ./media/mobile-engagement-windows-phone-get-started/campaign-first-params.png
[39]: ./media/mobile-engagement-windows-store-get-started/campaign-content.png
[40]: ./media/mobile-engagement-windows-store-get-started/campaign-create.png
[41]: ./media/mobile-engagement-windows-store-get-started/campaign-activate.png

<!--HONumber=47-->
