<properties
	pageTitle="Procedure di aggiornamento di Azure Mobile Engagement SDK per Web | Microsoft Azure"
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


# Procedure di aggiornamento di Azure Mobile Engagement SDK per Web

Se nell'applicazione Web è già stata integrata una versione precedente di Azure Mobile Engagement SDK per Web, è necessario considerare i seguenti punti quando si aggiorna l'SDK.

Se si sono superate numerose versioni di Mobile Engagement SDK per Web, potrebbe essere necessario completare diverse procedure durante il processo di aggiornamento. Ad esempio, se si esegue la migrazione dalla versione 1.4.0 alla 1.6.0, seguire innanzitutto le procedure per l'aggiornamento dalla versione 1.4.0 alla 1.5.0. Seguire quindi le procedure per l'aggiornamento dalla versione 1.5.0 alla 1.6.0.

Indipendentemente dalla versione dell'aggiornamento, sostituire qualsiasi versione precedente del file azure-engagement.js con la versione più recente dello stesso file.

## Aggiornamento da 1.2.1 a 2.0.0

Questa sezione illustra come eseguire la migrazione di un'integrazione di Mobile Engagement SDK per Web dal servizio Capptain offerto da Capptain SAS a un'app di Azure Mobile Engagement. Se si esegue la migrazione da una versione precedente, visitare il sito Web di Capptain per eseguire prima la migrazione alla versione 1.2.1 e quindi applicare la procedura seguente.

Questa versione di Mobile Engagement SDK per Web non supporta Samsung Smart TV, OperaTV, webOS o la funzionalità Reach.

>[AZURE.IMPORTANT] Capptain e Azure Mobile Engagement non costituiscono lo stesso servizio. La procedura seguente illustra solo come eseguire la migrazione dell'applicazione client. La migrazione di Mobile Engagement SDK per Web nell'app non comporta la migrazione dei dati dai server di Capptain a un server di Mobile Engagement.

### File JavaScript

Sostituire il file capptain-sdk.js con il file azure-engagement.js e aggiornare di conseguenza le importazioni degli script.

### Rimuovere Capptain Reach

Questa versione di Mobile Engagement SDK per Web non supporta la funzionalità Reach. Se nell'applicazione è integrata la funzione Capptain Reach,rimuoverla.

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

Rimuovere l'oggetto `reach` dalla configurazione, se esiste. L'aspetto sarà simile al seguente:

	window.capptain = {
	  [...]
	  reach: {
	    [...]
	  }
	}

Rimuovere eventuali altre personalizzazioni di Reach, ad esempio le categorie.

### Rimuovere API deprecate

Alcune delle API da Capptain sono deprecate in Mobile Engagement SDK per Web.

Rimuovere eventuali chiamate alle API seguenti: `agent.connect`, `agent.disconnect`, `agent.pause` e `agent.sendMessageToDevice`.

Rimuovere qualsiasi istanza dei callback seguenti dalla configurazione di Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived` e `onPushMessageReceived`.

### Configurazione

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

### API JavaScript

L'oggetto JavaScript globale `window.capptain` è stato rinominato `window.azureEngagement` ma è possibile usare l'alias `window.engagement` per le chiamate API. Non è possibile usare l'alias per definire la configurazione dell'SDK.

Ad esempio: `capptain.deviceId` diventa `engagement.deviceId`, `capptain.agent.startActivity` diventa `engagement.agent.startActivity` e così via.

<!---HONumber=AcomDC_0629_2016-->