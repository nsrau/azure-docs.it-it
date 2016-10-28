<properties
	pageTitle="Introduzione ad Azure Mobile Engagement per app Web | Microsoft Azure"
	description="Informazioni sull'uso di Azure Mobile Engagement con le funzionalità di analisi e notifiche push per le app Web."
	services="mobile-engagement"
	documentationCenter="Mobile"
	authors="piyushjo"
	manager=""
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="js"
	ms.topic="hero-article"
	ms.date="06/01/2016"
	ms.author="piyushjo" />

# Introduzione ad Azure Mobile Engagement per app Web

[AZURE.INCLUDE [Banner per la selezione del tipo di esercitazione](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Questo argomento illustra come usare Azure Mobile Engagement per conoscere l'utilizzo delle app Web.

Per completare questa esercitazione, è necessario disporre di:

+ Visual Studio 2015 o un altro editor di propria scelta
+ [Web SDK](http://aka.ms/P7b453) 

Web SDK è in anteprima e per il momento supporta solo l'analisi e non supporta ancora l'invio di notifiche push con il browser o in-app.

> [AZURE.NOTE] Per completare l'esercitazione, è necessario disporre di un account Azure attivo. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fit-IT%2Fdocumentation%2Farticles%2Fmobile-engagement-web-app-get-started).

##Configurare Mobile Engagement per l'app Web

[AZURE.INCLUDE [Creare l'app Mobile Engagement nel portale](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Connettere l'app al back-end di Mobile Engagement

Questa esercitazione presenta una "integrazione di base", che è la configurazione minima necessaria per raccogliere i dati.

Verrà creata un'app Web di base con Visual Studio per dare una dimostrazione dell'integrazione, ma è possibile eseguire i passaggi anche con qualsiasi applicazione Web creata al di fuori di Visual Studio.

###Creare una nuova app Web

I passaggi seguenti presuppongono l'utilizzo di Visual Studio 2015 anche se i passaggi sono simili nelle versioni precedenti di Visual Studio.

1. Avviare Visual Studio e selezionare **Nuovo progetto** nella schermata **Home**.

2. Nel popup selezionare **Web** -> **Applicazione Web ASP.NET**. Inserire **Nome**, **Percorso** e **Nome soluzione** dell'app e quindi fare clic su **OK**.

3. Nel popup **Selezionare un modello** selezionare **Vuoto** in **ASP.Net 4.5 Templates** (Modelli di ASP.Net 4.5) e fare clic su **OK**.

A questo punto è stato creato un nuovo progetto di app Web vuota in cui si integrerà Azure Mobile Engagement Web SDK.

###Connettere l'app al back-end di Mobile Engagement

1. Creare una nuova cartella denominata **javascript** nella soluzione e aggiungervi il file JS Web SDK **azure-engagement.js**. 

2. Aggiungere un nuovo file denominato **main.js** nella cartella javascript con il codice seguente. Verificare di aggiornare la stringa di connessione. Questo oggetto `azureEngagement` verrà usato per accedere ai metodi di Web SDK.

		var azureEngagement = {
		    debug: true,
		    connectionString: 'xxxxx'
		};

	![Visual Studio con file JS][1]

##Abilitare il monitoraggio in tempo reale

Per iniziare a inviare dati e assicurarsi che gli utenti siano attivi, è necessario inviare almeno un'attività al back-end di Mobile Engagement. Un'attività nell'ambito di un'app Web è una pagina Web.

1. Creare una nuova pagina denominata **home.html** nella soluzione e impostarla come pagina iniziale per l'app Web. 
2. Includere i due file JavaScript aggiunti prima in questa pagina aggiungendo il codice seguente nel tag body. 

	    <script type="text/javascript" src="javascript/main.js"></script>
	    <script type="text/javascript" src="javascript/azure-engagement.js"></script>

3. Aggiornare il tag body per chiamare il metodo `startActivity` di EngagementAgent.
		
		<body onload="engagement.agent.startActivity('Home')">

4. Ecco come appare il file **home.html**.
		
		<html>
		<head>
			...
		</head>
		<body onload="engagement.agent.startActivity('Home')">
		    <script type="text/javascript" src="javascript/main.js"></script>
		    <script type="text/javascript" src="javascript/azure-engagement.js"></script>
		</body>
		</html>

##Connettere l'app con monitoraggio in tempo reale

[AZURE.INCLUDE [Connettere l'app con monitoraggio in tempo reale](../../includes/mobile-engagement-connect-app-with-monitor.md)]

![][2]

##Estendere l'analisi

Ecco tutti i metodi attualmente disponibili con Web SDK che è possibile usare per l'analisi:

1. Attività/Pagine Web:

		engagement.agent.startActivity(name);
		engagement.agent.endActivity();

2. Events
		
		engagement.agent.sendEvent(name, extras);

3. Errori

		engagement.agent.sendError(name, extras);

4. Processi

		engagement.agent.startJob(name);
		engagement.agent.endJob(name);

<!-- Images. -->
[1]: ./media/mobile-engagement-web-app-get-started/visual-studio-solution-js.png
[2]: ./media/mobile-engagement-web-app-get-started/session.png

<!-----HONumber=AcomDC_0615_2016-->