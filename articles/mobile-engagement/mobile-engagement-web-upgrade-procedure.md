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

Se è già stata eseguita l'integrazione di una versione precedente dell'SDK nell'applicazione, sarà necessario tenere in considerazione gli aspetti seguenti durante l'aggiornamento dell'SDK.

Se non sono state applicate alcune versioni dell'SDK, potrebbe essere necessario eseguire più procedure. Se ad esempio si esegue la migrazione dalla versione 1.4.0 alla 1.6.0, sarà prima di tutto necessario eseguire la procedura per la migrazione "dalla 1.4.0 alla 1.5.0" e quindi la procedura per la migrazione "dalla 1.5.0 alla 1.6.0".

Indipendentemente dalla versione di partenza dell'aggiornamento, è necessario sostituire il file `azure-engagement.js` con il nuovo file.

## Dalla versione 1.2.1 alla 2.0.0

La sezione seguente illustra come eseguire la migrazione di un'integrazione dell'SDK dal servizio Capptain offerto da Capptain SAS a un'app basata su Azure Mobile Engagement. Se si esegue la migrazione da una versione precedente, visitare il sito Web di Capptain per eseguire prima la migrazione alla versione 1.2.1 e quindi applicare la procedura seguente.

Questa versione di Engagement SDK per Web non supporta samsung-tv, OperaTV, webOS e la funzionalità Reach.

>[AZURE.IMPORTANT] Capptain e Mobile Engagement sono servizi diversi e la procedura seguente illustra solo come eseguire la migrazione dell'app client. La migrazione dell'SDK nell'app NON comporta la migrazione dei dati dai server di Capptain ai server di Mobile Engagement.

### File JavaScript

Sostituire il file `capptain-sdk.js` con il file `azure-engagement.js` e aggiornare le importazioni degli script di conseguenza.

### Rimuovere Capptain Reach

Questa versione di Engagement SDK per Web non supporta la funzionalità Reach. Se lo strumento Capptain Reach è stato integrato nell'applicazione, è necessario rimuoverlo.

Rimuovere l'importazione di css Reach dalla pagina ed eliminare il file css correlato (per impostazione predefinita, capptain-reach.css).

Eliminare le risorse di Reach: l'immagine di chiusura (per impostazione predefinita, capptain-close.png) e l'icona del marchio (per impostazione predefinita, capptain-notification-icon).

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

Rimuovere l'interfaccia utente di Reach per annunci Web/testo e sondaggi. Il layout predefinito è simile al seguente:

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

Rimuovere l'oggetto `reach` dalla configurazione, se presente. Il layout è simile al seguente:

	window.capptain = {
	  [...]
	  reach: {
	    [...]
	  }
	}

Rimuovere eventuali altre personalizzazioni di Reach, ad esempio le categorie.

### Rimuovere API deprecate

Alcune delle API da Capptain sono deprecate nella versione Engagement dell'SDK.

Rimuovere eventuali chiamate alle API seguenti: `agent.connect`, `agent.disconnect`, `agent.pause`, `agent.sendMessageToDevice`.

Rimuovere i callback seguenti, se presenti, dalla configurazione di Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived`, `onPushMessageReceived`.

### Configurazione

Il servizio Engagement usa ora una stringa di connessione per configurare gli identificatori dell'SDK, ad esempio l'identificatore dell'applicazione.

Sostituire l'ID dell'applicazione con la stringa di connessione. Si noti anche che l'oggetto globale per la configurazione dell'SDK passa da `capptain` a `azureEngagement`.

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

L'oggetto JavaScript globale `window.capptain` è stato rinominato `window.azureEngagement` ma è possibile usare l'alias `window.engagement` per le chiamate API (non è possibile usare l'alias per definire la configurazione dell'SDK).

Ad esempio: `capptain.deviceId` diventa `engagement.deviceId`, `capptain.agent.startActivity` diventa `engagement.agent.startActivity` ecc ...

<!---HONumber=AcomDC_0615_2016-->