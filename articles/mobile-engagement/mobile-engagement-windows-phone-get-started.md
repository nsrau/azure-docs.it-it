<properties
	pageTitle="Introduzione ad Azure Mobile Engagement per app per Windows Phone Silverlight"
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
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Introduzione ad Azure Mobile Engagement per app per Windows Phone Silverlight

[AZURE.INCLUDE [Banner per la selezione del tipo di esercitazione](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'utilizzo dell'app e inviare notifiche push a utenti segmentati di un'applicazione per Windows Phone Silverlight. Questa esercitazione illustra uno scenario di trasmissione semplice tramite Mobile Engagement. Si creerà un'app per Windows Phone Silverlight vuota che raccoglie dati di base e riceve notifiche push tramite il Servizio notifica push Microsoft (MPNS).

> [AZURE.NOTE] Se si usa Windows Phone 8.1 (non Silverlight) come piattaforma di destinazione, fare riferimento all'[esercitazione per le app di Windows universali](mobile-engagement-windows-store-dotnet-get-started.md).

Per completare questa esercitazione, è necessario disporre di:

+ Visual Studio 2013
+ Pacchetto Nuget [MicrosoftAzure.MobileEngagement]

> [AZURE.NOTE] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdocumentation%2Farticles%2Fmobile-engagement-windows-phone-get-started).

##<a id="setup-azme"></a>Configurare Mobile Engagement per l'app per Windows Phone

[AZURE.INCLUDE [Creare l'app Mobile Engagement nel portale](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Connettere l'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati e inviare una notifica push. La documentazione relativa all'integrazione completa è disponibile nella [Integrazione di Mobile Engagement SDK per Windows Phone](../mobile-engagement-windows-phone-sdk-overview/)

Si creerà un'app di base con Visual Studio per illustrare l'integrazione.

###Creare un nuovo progetto Windows Phone Silverlight

I passaggi seguenti presuppongono l'utilizzo di Visual Studio 2015 anche se i passaggi sono simili nelle versioni precedenti di Visual Studio.

1. Avviare Visual Studio e selezionare **Nuovo progetto** nella schermata **Home**.

2. Nella finestra popup, selezionare **Windows 8** -> **Windows Phone** -> **Applicazione vuota (Silverlight per Windows Phone)**. Inserire **Nome** e **Nome soluzione** dell’applicazione, quindi fare clic su **OK**.

    ![][1]

3. È possibile scegliere la versione **Windows Phone 8.0** o **Windows Phone 8.1** come piattaforma di destinazione.

A questo punto è stata creata una nuova app per Windows Phone Silverlight in cui si integrerà Azure Mobile Engagement SDK.

###Connettere l'app al back-end di Mobile Engagement

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

##<a id="monitor"></a>Abilitare il monitoraggio in tempo reale

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno una schermata (Activity) al back-end di Mobile Engagement.

1. Nel MainPage.xaml.cs, aggiungere l’istruzione `using`:

    	using Microsoft.Azure.Engagement;

2. Sostituire la classe di base di **MainPage**, che si trova prima di **PhoneApplicationPage**, con **EngagementPage**.

    	class MainPage : EngagementPage 
	
3. Nel file `MainPage.xml`:

	a. Aggiungere le dichiarazioni di spazi dei nomi:

	   	 xmlns:engagement="clr-namespace:Microsoft.Azure.Engagement;assembly=Microsoft.Azure.Engagement.EngagementAgent.WP"

	b. Sostituire `phone:PhoneApplicationPage` nel nome del tag XML con `engagement:EngagementPage`

##<a id="monitor"></a>Connettere l'app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connettere l'app con monitoraggio in tempo reale](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Abilitare le notifiche push e la messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto di campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Le sezioni seguenti consentono di configurare l'app per la ricezione.

###Abilitare l'app alla ricezione di notifiche push MPNS

Aggiungere nuove funzionalità al file `WMAppManifest.xml`:

		ID_CAP_PUSH_NOTIFICATION
		ID_CAP_WEBBROWSERCOMPONENT

   ![][5]

###Inizializzare REACH SDK

1. In `App.xaml.cs` chiamare `EngagementReach.Instance.Init();` nella funzione **Application\_Launching** subito dopo l'inizializzazione dell'agente:

		private void Application_Launching(object sender, LaunchingEventArgs e)
		{
		   EngagementAgent.Instance.Init();
		   EngagementReach.Instance.Init();
		}

2. In `App.xaml.cs` chiamare `EngagementReach.Instance.OnActivated(e);` nella funzione **Application\_Activated** subito dopo l'inizializzazione dell'agente:

		private void Application_Activated(object sender, ActivatedEventArgs e)
		{
		   EngagementAgent.Instance.OnActivated(e);
		   EngagementReach.Instance.OnActivated(e);
		}

Le impostazioni sono state completate. Ora è necessario verificare di avere eseguito correttamente l'integrazione di base.

##<a id="send"></a>Inviare una notifica all'app

[AZURE.INCLUDE [Creare una campagna Push Windows](../../includes/mobile-engagement-windows-push-campaign.md)]

Si dovrebbe ora vedere una notifica sul dispositivo che verrà visualizzata come una notifica all'interno dell'app se l'app è aperta in caso contrario come una notifica popup come quella seguente:

![][6]

<!-- URLs. -->
[MicrosoftAzure.MobileEngagement]: http://go.microsoft.com/?linkid=9874664
[Mobile Engagement Windows Phone SDK documentation]: ../mobile-engagement-windows-phone-integrate-engagement/

<!-- Images. -->
[1]: ./media/mobile-engagement-windows-phone-get-started/project-properties.png
[2]: ./media/mobile-engagement-windows-phone-get-started/wmappmanifest-capabilities.png
[3]: ./media/mobile-engagement-windows-phone-get-started/add-connection-string.png
[5]: ./media/mobile-engagement-windows-phone-get-started/reach-capabilities.png
[6]: ./media/mobile-engagement-windows-phone-get-started/push-screenshot.png

<!---HONumber=AcomDC_0330_2016-->