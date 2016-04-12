<properties
	pageTitle="Introduzione ad Azure Mobile Engagement per la distribuzione di Unity in iOS"
	description="Informazioni sull'uso di Azure Mobile Engagement con funzionalità di analisi e notifiche push per le app Unity distribuite in dispositivi iOS."
	services="mobile-engagement"
	documentationCenter="unity"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-unity-ios"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/25/2016"
	ms.author="piyushjo" />

# Introduzione ad Azure Mobile Engagement per la distribuzione di Unity in iOS

[AZURE.INCLUDE [Banner per la selezione del tipo di esercitazione](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Questo argomento descrive come usare Azure Mobile Engagement per ottenere informazioni sull'uso dell'app e sull'invio di notifiche push a utenti segmentati di un'applicazione Unity durante lo sviluppo in un dispositivo iOS. Questa esercitazione fa uso della classica esercitazione Roll-a-ball di Unity come punto di partenza. Seguire i passaggi dell'[esercitazione Roll-a-ball](mobile-engagement-unity-roll-a-ball.md) prima di procedere con l'integrazione di Mobile Engagement dimostrata nell'esercitazione seguente.

Per completare questa esercitazione, è necessario disporre di:

+ [Editor di Unity](http://unity3d.com/get-unity)
+ [Mobile Engagement Unity SDK](https://aka.ms/azmeunitysdk)
+ Editor di Xcode

> [AZURE.NOTE] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdocumentation%2Farticles%2Fmobile-engagement-unity-ios-get-started).

##<a id="setup-azme"></a>Configurare Mobile Engagement per l'app iOS

[AZURE.INCLUDE [Creare l'app Mobile Engagement nel portale](../../includes/mobile-engagement-create-app-in-portal.md)]

##<a id="connecting-app"></a>Connettere l'app al back-end di Mobile Engagement

###Importare il pacchetto Unity

1. Scaricare il [pacchetto Unity per Mobile Engagement](https://aka.ms/azmeunitysdk) e salvarlo nel computer locale. 

2. Passare a **Assets -> Import Package -> Custom Package** e selezionare il pacchetto scaricato nel passaggio precedente.

	![][70]

3. Assicurarsi che tutti i file siano selezionati e fare clic sul pulsante **Import**.

	![][71]

4. Al termine dell'importazione verranno visualizzati i file dell'SDK importati nel progetto.

	![][72]

###Aggiornare EngagementConfiguration

1. Aprire il file di script **EngagementConfiguration** dalla cartella dell'SDK e aggiornare **IOS\_CONNECTION\_STRING** con la stringa di connessione ottenuta in precedenza dal portale di Azure.  

	![][73]

2. Salvare il file.

###Configurare l'app per il rilevamento di base

1. Aprire lo script **PlayerController** collegato all'oggetto Player per la modifica. 

2. Aggiungere l'istruzione using seguente:

		using Microsoft.Azure.Engagement.Unity;

3. Aggiungere quanto segue al metodo `Start()`:
    
        EngagementAgent.Initialize();
        EngagementAgent.StartActivity("Home");

###Distribuire ed eseguire l'app

1. Connettere un dispositivo iOS al computer. 

2. Aprire **File -> Build Settings**.

	![][40]

3. Selezionare **iOS** e quindi fare clic su **Switch Platform**.

	![][41]

	![][42]

4. Fare clic su **Player Settings** e fornire un identificatore del bundle valido.

	![][53]

5. Infine, fare clic su **Build And Run**.

	![][54]

6. Potrebbe essere necessario specificare un nome della cartella in cui archiviare il pacchetto iOS.

	![][43]

7. Se tutto va bene, il progetto verrà compilato e sarà possibile aprirlo nell'applicazione Xcode.

8. Assicurarsi che l'**identificatore del bundle** sia corretto nel progetto.

	![][75]

10. A questo punto, eseguire l'app in Xcode in modo che il pacchetto venga distribuito nel dispositivo connesso. Il gioco Unity dovrebbe essere disponibile nel telefono.

##<a id="monitor"></a>Connettere l'app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connettere l'app con monitoraggio in tempo reale](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Abilitare le notifiche push e la messaggistica in-app

Mobile Engagement consente di interagire con gli utenti e coinvolgerli tramite notifiche push e messaggistica in-app nel contesto delle campagne. Questo modulo è denominato REACH nel portale di Mobile Engagement. Non è necessario eseguire altre operazioni di configurazione nell'app per ricevere notifiche, perché è già configurata.

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[40]: ./media/mobile-engagement-unity-ios-get-started/40.png
[41]: ./media/mobile-engagement-unity-ios-get-started/41.png
[42]: ./media/mobile-engagement-unity-ios-get-started/42.png
[43]: ./media/mobile-engagement-unity-ios-get-started/43.png
[53]: ./media/mobile-engagement-unity-ios-get-started/53.png
[54]: ./media/mobile-engagement-unity-ios-get-started/54.png
[70]: ./media/mobile-engagement-unity-ios-get-started/70.png
[71]: ./media/mobile-engagement-unity-ios-get-started/71.png
[72]: ./media/mobile-engagement-unity-ios-get-started/72.png
[73]: ./media/mobile-engagement-unity-ios-get-started/73.png
[74]: ./media/mobile-engagement-unity-ios-get-started/74.png
[75]: ./media/mobile-engagement-unity-ios-get-started/75.png

<!---HONumber=AcomDC_0330_2016-->