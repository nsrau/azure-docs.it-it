<properties
	pageTitle="Integrazione di Azure Mobile Engagement SDK per Web | Microsoft Azure"
	description="Ultimi aggiornamenti e procedure relativi all'SDK per Web per Azure Mobile Engagement"
	services="mobile-engagement"
	documentationCenter="mobile"
	authors="piyushjo"
	manager="erikre"
	editor="" />

<tags
	ms.service="mobile-engagement"
	ms.workload="mobile"
	ms.tgt_pltfrm="web"
	ms.devlang="js"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="piyushjo" />

#Come integrare Engagement in un'applicazione Web

> [AZURE.SELECTOR]
- [Windows Universal](mobile-engagement-windows-store-integrate-engagement.md)
- [Windows Phone Silverlight](mobile-engagement-windows-phone-integrate-engagement.md)
- [iOS](mobile-engagement-ios-integrate-engagement.md)
- [Android](mobile-engagement-android-integrate-engagement.md)

Questa procedura descrive il modo più semplice per attivare le funzioni di analisi e monitoraggio di Engagement in un'applicazione Web.

I passaggi seguenti sono sufficienti per attivare la segnalazione dei log necessari per calcolare tutte le statistiche relative a utenti, sessioni, attività, arresti anomali del sistema e dati tecnici. La segnalazione dei log necessari per calcolare altre statistiche quali eventi, errori e processi deve essere eseguita manualmente mediante l'API di Engagement (vedere [Come usare l'API di Engagement in un'applicazione Web](mobile-engagement-web-use-engagement-api.md)) poiché queste statistiche dipendono dall'applicazione.

## Introduzione

Scaricare l'SDK Web da [qui](http://aka.ms/P7b453). L'SDK viene fornito come un singolo file JavaScript denominato **azure-engagement.js** che deve essere incluso in ogni pagina dell'applicazione Web o del sito.

Questo script **DEVE** essere caricato **DOPO** uno script o un frammento di codice che deve essere scritto per configurare Engagement per l'applicazione.

## Compatibilità dei browser

Engagement SDK per Web usa la codifica/decodifica JSON nativa e le richieste AJAX tra domini (basandosi sulla specifica W3C CORS).

* Edge 12+
* IE 10+
* Firefox 3.5+
* Chrome 4+
* Safari 6+
* Opera 12+

## Configurare Engagement

Scrivere uno script per creare un oggetto JavaScript **azureEngagement** globale simile al seguente.
 
Dal momento che il sito potrebbe contenere più pagine, questo esempio presuppone che lo script venga anche incluso in tutte le pagine e in questa procedura sarà denominato `azure-engagement-conf.js`.

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	};

L'elemento `connectionString` per l'applicazione viene visualizzato nel portale di Azure.

> [AZURE.NOTE] `appVersionName` e `appVersionCode` sono facoltativi, si consiglia di configurarli per consentire all'analisi di elaborare le informazioni sulla versione.

## Includere script di Engagement nelle pagine

	<head>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</head>

Oppure

	<body>
	  ...
	  <script type="text/javascript" src="azure-engagement-conf.js"></script>
	  <script type="text/javascript" src="azure-engagement.js"></script>
	  ...
	</body>

## Alias

Dopo il caricamento, lo script SDK crea l'alias **engagement** per accedere alle API SDK (non può essere usato per definire la configurazione dell'SDK). In questa documentazione l'alias verrà usato come riferimento.

Si noti che se l'alias predefinito è in conflitto con un'altra variabile globale dalla pagina, sarà possibile ridefinirlo nella configurazione prima di caricare l'SDK nel modo seguente:

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  appVersionName: '1.0.0',
	  appVersionCode: 1
	  alias:'anotherAlias'
	};

## Segnalazione di base

### Monitoraggio di una sessione

Una sessione di Engagement è suddivisa in una sequenza di attività identificate da un nome.

In un sito Web classico è consigliabile dichiarare un'attività diversa in ogni pagina del sito. In un sito Web o un'applicazione Web che non cambia mai la pagina corrente è possibile tenere traccia delle attività in modo più preciso.

In entrambi i casi, per avviare o modificare l'attività utente corrente, chiamare la funzione `engagement.agent.startActivity` come nell'esempio seguente:

	<body onload="yourOnload()">

	<!-- -->

	yourOnload = function() {
      [...]
      engagement.agent.startActivity('welcome');
	};

Il server di Engagement terminerà automaticamente una sessione aperta entro 3 minuti dalla chiusura della pagina dell'applicazione.

In alternativa è anche possibile terminare una sessione manualmente tramite una chiamata alla funzione `engagement.agent.endActivity`, il che determinerà l'impostazione dell'attività utente corrente su "inattiva" terminando effettivamente la sessione dopo 10 secondi, a meno che, nel frattempo, una nuova chiamata alla funzione `engagement.agent.startActivity` ripristini la sessione.
 
Questo ritardo di 10 secondi può essere configurato nell'oggetto **engagement** globale:

	engagement.sessionTimeout = 2000; // 2 seconds
	// or
	engagement.sessionTimeout = 0; // end the session as soon as endActivity is called

> [AZURE.NOTE] Non è possibile usare la funzione `engagement.agent.endActivity` nel callback `onunload` dal momento che le chiamate Ajax non sono possibili in questa fase.

## Segnalazione avanzata

Facoltativamente, per segnalare `events`, `errors` e `jobs` specifici dell'applicazione, è necessario usare l'API di Engagement mediante l'oggetto `engagement.agent`.

L'API di Engagement consente di usare tutte le funzionalità avanzate di Engagement ed è descritta in dettaglio nell'argomento [Come usare l'API di Engagement in un'applicazione Web](mobile-engagement-web-use-engagement-api.md).

## Personalizzare gli URL usati per le chiamate AJAX

È possibile personalizzare gli URL usati dall'SDK. Ad esempio, per ridefinire l'URL di accesso (endpoint SDK per l'accesso), è possibile eseguire l'override della configurazione in modo simile al seguente:

	window.azureEngagement = {
	  ...
	  urls: {
	    ...        
	    getLoggerUrl: function() {
	    return 'someProxy/log';
	    }
	  }
	};

Se le funzioni URL restituiscono una stringa che inizia con `/`, `//`, `http://` o `https://`, lo schema predefinito non è usato.

Per impostazione predefinita, per tali URL è usato lo schema `https://`. Se si desidera personalizzare lo schema predefinito, eseguire l'override della configurazione in modo simile al seguente:

	window.azureEngagement = {
	  ...
	  urls: {
        ...	     
	    scheme: '//'
	  }
	};

<!---HONumber=AcomDC_0615_2016-->