---
title: Come usare l'assegnazione di tag di versione con un modello di apprendimento di conversazioni - servizi cognitivi di Azure | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare il controllo delle versioni e l'assegnazione di tag con un modello di Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ms.openlocfilehash: 4067c7fb43cc200b8f49dbc14151c69a188e4e8e
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66475716"
---
# <a name="how-to-use-version-tagging"></a>Come usare l'assegnazione di tag alle versioni

Questa esercitazione illustra come assegnare tag alle versioni del modello di Conversation Learner e impostare la versione "live".  

## <a name="requirements"></a>Requisiti
Questa esercitazione richiede l'uso di Bot Framework Emulator per creare dialoghi di log, non l'interfaccia utente Web dei dialoghi di log.  

Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione:

    npm run tutorial-general

## <a name="details"></a>Dettagli

Le versioni con tag del modello sono statiche e non possono essere modificate. Quando si modifica il modello, si modifica sempre la versione master. Quando si aggiunge un nuovo tag, Conversation Learner acquisisce uno snapshot del modello in quel momento. 

Il bot userà la versione del modello selezionata come versione "Live", ma qualsiasi conversazione sarà visibile solo quando la proprietà "tag di modifica" è impostata su "Master". Se la proprietà "tag di modifica" del modello è impostata su un valore diverso da "Master", è possibile visualizzare, ma non modificare in alcun modo, lo snapshot del modello.

## <a name="steps"></a>Passaggi

### <a name="install-the-bot-framework-emulator"></a>Installare Bot Framework Emulator.

1. Passare a [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
2. Scaricare e installare l'emulatore.

### <a name="create-a-model"></a>Creare il modello

1. Nella home page di elenco dei modelli fare clic sul pulsante `New Model` (Nuovo modello).
2. Nel campo `Name` digitare "Tutorial-18-Versioning" e premere INVIO.
4. Nel riquadro a sinistra fare clic su "Settings" (Impostazioni).
5. Copiare il contenuto del campo CONVERSATION_LEARNER_MODEL_ID negli Appunti.

### <a name="configure-the-emulator"></a>Configurare l'emulatore.

1. Nella cartella radice di Conversation Learner aprire il file con estensione env.
2. Aggiungere una riga nel file "con estensione env" simile alla seguente:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Riavviare il servizio Conversation Learner uscendo dal prompt dei comandi ed eseguendo di nuovo questo comando:
    - `npm run tutorial-general`
4. In Bot Framework Emulator creare una nuova configurazione del bot, impostare l'URL dell'endpoint su `http://localhost:3978/api/messages`

### <a name="version-1"></a>Versione 1

Si creerà una singola azione per la versione 1.

1. Nel riquadro a sinistra dell'interfaccia utente Web fare clic su "Actions" (Azioni), quindi sul pulsante `New Action` (Nuova azione).
2. Nel campo "Bot's response..." (Risposta del bot...) immettere "hi there (version 1)".
3. Fare clic sul pulsante `Save`.

La versione verrà contrassegnata come "versione 1" del modello.

1. Nel riquadro a sinistra fare clic su "settings" (impostazioni), quindi sull'icona dei tag di versione ![](../media/tutorial18_version_tags.PNG) per visualizzare il pulsante `New Tag` (Nuovo tag) su cui fare clic.
    - Assegnare il nome "version 1".
1. Nel menu a discesa dei "tag live" selezionare "Version 1".  
    - A questo punto i canali che usano questo bot useranno la versione 1 del modello.
    - L'entità, le azioni e i dialoghi di training di questo modello della versione 1 non possono più essere modificati.
    - Se si seleziona "Version 1" come "tag di modifica" sarà possibile SOLO visualizzare il modello e non modificarlo.
    - Lasciare "tag di modifica" impostato su "Master", che è l'unica versione del modello che può essere modificata.

A questo punto "Version 1" sarà visibile nella griglia dei "tag di versione".

### <a name="version-2"></a>Versione 2

Ora si modificherà il modello per distinguerlo dalla versione 1.

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni).
2. Nella griglia delle azioni fare clic su "hi there (version 1)".
3. Modificare il valore del campo "Bot's response..." (Risposta del bot...) in "hi there (version 2)".
4. Fare clic sul pulsante `Save`.
5. Fare clic sul pulsante `New Action`.
6. Nel campo "Bot's response..." (Risposta del bot...) digitare "bye bye (version 2)".

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Confermare che Bot Framework Emulator sta usando la versione 1.

1. In Bot Framework Emulator digitare il messaggio "Hey there".
2. Si noti che il bot risponde con "hi there (version 1)".
    - Questa risposta conferma che la versione 1 è "live".

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>Visualizzare i log di conversazione nell'interfaccia utente Web di Conversation Learner

1. Nel riquadro a sinistra fare clic su "Log Dialogs" (Dialoghi di log).
    - Se non viene visualizzato alcun dialogo, fare clic sul pulsante per aggiornare.
2. Si noti il tag "Version 1" nella griglia.
3. Nella griglia fare clic su "hi there (version 1)"

> [!NOTE]
> È possibile apportare correzioni scegliendo tra tutte le funzionalità di Conversation Learner attualmente disponibili, tuttavia, queste modifiche saranno apportate nel Master e non nella versione 1.

È stato illustrato il funzionamento del controllo delle versioni e come è possibile interagire con il bot usando Bot Framework Emulator.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Enumerazione entità e Set di azioni dell'entità](./tutorial-enum-set-entity.md)
