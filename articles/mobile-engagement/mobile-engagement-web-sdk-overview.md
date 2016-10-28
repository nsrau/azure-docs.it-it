<properties
	pageTitle="Panoramica su Azure Mobile Engagement SDK per Web | Microsoft Azure"
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
	ms.date="06/07/2016"
	ms.author="piyushjo" />


# Azure Mobile Engagement Web SDK

Da qui è possibile visualizzare tutti i dettagli su come integrare Azure Mobile Engagement in un'app Web. Per fare una prova prima di iniziare con la propria app Web, vedere l'[esercitazione di 15 minuti](mobile-engagement-web-app-get-started.md).

## Procedure di integrazione
1. Informazioni su [come integrare Mobile Engagement in un'app Web](mobile-engagement-web-integrate-engagement.md).

2. Per l'implementazione del piano di tag, vedere [come usare l'API avanzata di assegnazione tag di Mobile Engagement nella propria app Web](mobile-engagement-web-use-engagement-api.md).

## Note sulla versione

### 2\.0.1 (10/6/2016)

-   Disabilitato Mobile Engagement Web SDK in Internet Explorer 8 e Internet Explorer 9.
-   Corretto il rilevamento del Web browser Opera.

Per tutte le versioni, vedere le [note sulla versione complete](mobile-engagement-web-release-notes.md).

## Procedure di aggiornamento

### Aggiornamento da 1.2.1 a 2.0.0

Le sezioni seguenti illustrano come eseguire la migrazione di un'integrazione di Mobile Engagement Web SDK dal servizio Capptain offerto da Capptain SAS a un'app di Azure Mobile Engagement. Se si esegue la migrazione da una versione precedente alla 1.2.1, visitare il sito Web di Capptain per eseguire prima la migrazione alla versione 1.2.1 e quindi applicare le procedure seguenti.

Questa versione di Mobile Engagement SDK per Web non supporta Samsung Smart TV, OperaTV, webOS o la funzionalità Reach.

>[AZURE.IMPORTANT] Capptain e Azure Mobile Engagement sono servizi diversi e le procedure seguente illustrano solo come eseguire la migrazione dell'app client. La migrazione di Mobile Engagement SDK per Web nell'app non comporta la migrazione dei dati dai server di Capptain a un server di Mobile Engagement.

#### File JavaScript

Sostituire il file capptain-sdk.js con il file azure-engagement.js e aggiornare di conseguenza le importazioni degli script.

#### Rimuovere Capptain Reach

Questa versione di Mobile Engagement SDK per Web non supporta la funzionalità Reach. Se nell'applicazione è integrata la funzione Capptain Reach, rimuoverla.

Rimuovere l'importazione di CSS Reach dalla pagina ed eliminare il relativo file con estensione css (per impostazione predefinita, capptain-reach.css).

Eliminare le risorse di Reach seguenti: l'immagine di chiusura (per impostazione predefinita, capptain-close.png) e l'icona del marchio (per impostazione predefinita, capptain-notification-icon).

Rimuovere l'interfaccia utente di Reach per le notifiche in-app. Il layout predefinito è simile al seguente:

	<!-- capptain notification -->
	<div id="capptain_notification_area" class="capptain_category_default">
	  <div class="icon">
	    <img src="capptain-notification-icon.png" alt="icon" />
	  </div>
	  <div class="content">
	    <div class="title" id="capptain_notification_title"></div>
	    <div class="message" id="capptain_notification_message"></div>
	  </div>
	  <div id="capptain_notification_image"></div>
	  <div>
	    <button id="capptain_notification_close">Close</button>
	  </div>
	</div>

Rimuovere l'interfaccia utente di Reach per gli annunci Web, per gli annunci di testo e per i sondaggi. Il layout predefinito è simile al seguente:

	<div id="capptain_overlay" class="capptain_category_default">
	  <button id="capptain_overlay_close">x</button>
	  <div id="capptain_overlay_title"></div>
	  <div id="capptain_overlay_body"></div>
	  <div id="capptain_overlay_poll"></div>
	  <div id="capptain_overlay_buttons">
	    <button id="capptain_overlay_exit"></button>
	    <button id="capptain_overlay_action"></button>
	  </div>
	</div>

Rimuovere l'oggetto `reach` dalla configurazione, se presente. L'aspetto sarà simile al seguente:

	window.capptain = {
	  [...]
	  reach: {
	    [...]
	  }
	}

Rimuovere eventuali altre personalizzazioni di Reach, ad esempio le categorie.

#### Rimuovere API deprecate

Alcune delle API da Capptain sono deprecate in Mobile Engagement SDK per Web.

Rimuovere eventuali chiamate alle API seguenti: `agent.connect`, `agent.disconnect`, `agent.pause` e `agent.sendMessageToDevice`.

Rimuovere i callback seguenti dalla configurazione di Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived` e `onPushMessageReceived`.

#### Configurazione

Mobile Engagement usa ora una stringa di connessione per configurare gli identificatori dell'SDK, ad esempio l'identificatore dell'applicazione.

Sostituire l'ID dell'applicazione con la stringa di connessione. Si noti che l'oggetto globale per la configurazione dell'SDK cambia da `capptain` a `azureEngagement`.

Prima della migrazione:

	window.capptain = {
	  appId: ...,
	  [...]
	};

Dopo la migrazione:

	window.azureEngagement = {
	  connectionString: 'Endpoint={appCollection}.{domain};AppId={appId};SdkKey={sdkKey}',
	  [...]
	};

La stringa di connessione per l'applicazione viene visualizzata nel portale di Azure.

#### API JavaScript

L'oggetto JavaScript globale `window.capptain` è stato rinominato `window.azureEngagement`, ma è possibile usare l'alias `window.engagement` per le chiamate API. Non è possibile usare l'alias per definire la configurazione dell'SDK.

Ad esempio, `capptain.deviceId` diventa `engagement.deviceId`, `capptain.agent.startActivity` diventa `engagement.agent.startActivity` e così via.

Se nell'applicazione Web è già stata integrata una versione precedente di Azure Mobile Engagement Web SDK, vedere le [procedure di aggiornamento](mobile-engagement-web-upgrade-procedure.md).

<!---HONumber=AcomDC_0713_2016-->