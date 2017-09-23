---
title: Panoramica di Azure Mobile Engagement SDK per Web | Documentazione Microsoft
description: Ultimi aggiornamenti e procedure relativi all'SDK per Web per Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 5bbc2fda-0f3f-43d0-a73d-0f2c0f8dc25b
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: web
ms.devlang: js
ms.topic: article
ms.date: 10/18/2016
ms.author: piyushjo
ms.translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 770a83131a3e661771db50b22ce7de25b2d541cf
ms.contentlocale: it-it
ms.lasthandoff: 11/17/2016

---
# <a name="azure-mobile-engagement-web-sdk"></a>Azure Mobile Engagement Web SDK
Da qui è possibile visualizzare tutti i dettagli su come integrare Azure Mobile Engagement in un'app Web. Per fare una prova prima di iniziare con la propria app Web, vedere l' [esercitazione di 15 minuti](mobile-engagement-web-app-get-started.md).

## <a name="integration-procedures"></a>Procedure di integrazione
1. Informazioni su [come integrare Mobile Engagement in un'app Web](mobile-engagement-web-integrate-engagement.md).
2. Per l'implementazione del piano di tag, vedere [come usare l'API avanzata di assegnazione tag di Mobile Engagement nella propria app Web](mobile-engagement-web-use-engagement-api.md).

## <a name="release-notes"></a>Note sulla versione
### <a name="202-10182016"></a>2.0.2 (18/10/2016)
* Correzione di un problema di arresto anomalo nell'esplorazione privata (Safari).
* Correzione di un problema di arresto anomalo nei browser con cookie disabilitati.

Per tutte le versioni, vedere le [note sulla versione complete](mobile-engagement-web-release-notes.md).

## <a name="upgrade-procedures"></a>Procedure di aggiornamento
### <a name="upgrade-from-121-to-200"></a>Aggiornamento da 1.2.1 a 2.0.0
Le sezioni seguenti illustrano come eseguire la migrazione di un'integrazione di Mobile Engagement Web SDK dal servizio Capptain offerto da Capptain SAS a un'app di Azure Mobile Engagement. Se si esegue la migrazione da una versione precedente alla 1.2.1, visitare il sito Web di Capptain per eseguire prima la migrazione alla versione 1.2.1 e quindi applicare le procedure seguenti.

Questa versione di Mobile Engagement SDK per Web non supporta Samsung Smart TV, OperaTV, webOS o la funzionalità Reach.

> [!IMPORTANT]
> Capptain e Azure Mobile Engagement sono servizi diversi e le procedure seguente illustrano solo come eseguire la migrazione dell'app client. La migrazione di Mobile Engagement SDK per Web nell'app non comporta la migrazione dei dati dai server di Capptain a un server di Mobile Engagement.
> 
> 

#### <a name="javascript-files"></a>File JavaScript
Sostituire il file capptain-sdk.js con il file azure-engagement.js e aggiornare di conseguenza le importazioni degli script.

#### <a name="remove-capptain-reach"></a>Rimuovere Capptain Reach
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

Rimuovere l'oggetto `reach` dalla configurazione, se esiste. L'aspetto sarà simile al seguente:

    window.capptain = {
      [...]
      reach: {
        [...]
      }
    }

Rimuovere eventuali altre personalizzazioni di Reach, ad esempio le categorie.

#### <a name="remove-deprecated-apis"></a>Rimuovere API deprecate
Alcune delle API da Capptain sono deprecate in Mobile Engagement SDK per Web.

Rimuovere eventuali chiamate alle API seguenti: `agent.connect`, `agent.disconnect`, `agent.pause` e `agent.sendMessageToDevice`.

Rimuovere i callback seguenti dalla configurazione di Capptain: `onConnected`, `onDisconnected`, `onDeviceMessageReceived` e `onPushMessageReceived`.

#### <a name="configuration"></a>Configurazione
Mobile Engagement usa ora una stringa di connessione per configurare gli identificatori dell'SDK, ad esempio l'identificatore dell'applicazione.

Sostituire l'ID dell'applicazione con la stringa di connessione. Tenere presente che l'oggetto globale per le modifiche alla configurazione dell'SDK cambia da `capptain` a `azureEngagement`.

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

#### <a name="javascript-apis"></a>API JavaScript
L'oggetto JavaScript globale `window.capptain` è stato rinominato `window.azureEngagement`, ma è possibile usare l'alias `window.engagement` per le chiamate API. Non è possibile usare l'alias per definire la configurazione dell'SDK.

Ad esempio, `capptain.deviceId` diventa `engagement.deviceId`, `capptain.agent.startActivity` diventa `engagement.agent.startActivity` e così via.

Se nell'applicazione Web è già stata integrata una versione precedente di Azure Mobile Engagement Web SDK, vedere le [procedure di aggiornamento](mobile-engagement-web-upgrade-procedure.md).


