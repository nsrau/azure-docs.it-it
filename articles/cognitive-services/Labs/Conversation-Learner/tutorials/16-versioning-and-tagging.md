---
title: Come usare il controllo delle versioni e l'assegnazione di tag con un'applicazione di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare il controllo delle versioni e l'assegnazione di tag con un'applicazione di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: ea013db078ff33f8597b0e15a8fc951e8ae320e8
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35376284"
---
# <a name="how-to-use-versioning-and-tagging"></a>Come usare il controllo delle versioni e l'assegnazione di tag

Questa esercitazione illustra come assegnare tag alle versioni dell'app di Conversation Learner e impostare la versione "live".  

## <a name="requirements"></a>Requisiti
Per creare i dialoghi di log, questa esercitazione richiede l'uso dell'emulatore del bot, non dell'interfaccia utente Web dei dialoghi di log.  

Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione:

    npm run tutorial-general

## <a name="details"></a>Dettagli

La modifica viene sempre eseguita sul tag denominato "master". È possibile creare versioni con tag dal master (usando essenzialmente uno snapshot del master), ma non modificare le versioni con tag.

## <a name="steps"></a>Passaggi

### <a name="install-the-bot-framework-emulator"></a>Installare Bot Framework Emulator

- Passare a [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
- Scaricare e installare l'emulatore.

### <a name="create-an-app"></a>Creare un'app

1. Fare clic su New App (Nuova app)
2. Nel campo Name (Nome) immettere "Tutorial-16-Versioning"
3. Click Create 
4. Fare clic su Impostazioni
5. Copiare l'ID app

### <a name="configure-the-emulator"></a>Configurare l'emulatore

- Nella cartella radice di Conversation Learner aprire il file con estensione env.
- Incollare l'ID app come valore di CONVERSATION_LEARNER_APP_ID.
- Riavviare il servizio Conversation Learner uscendo dal prompt dei comandi ed eseguendo di nuovo questo comando:
 
    npm run tutorial-general 

### <a name="actions"></a>Azioni

Creare un'azione:

1. Passare all'interfaccia utente Web.
1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione).
2. In Response (Risposta) immettere "hi there (version 1)".
3. Fare clic su Save.


![](../media/tutorial16_action1.PNG)

Creare un nuovo tag:

3. Fare clic su "Settings" (Impostazioni) e creare un nuovo tag.
    - Denominare il tag "version 1".
4. Impostare "version 1" come "live".  
    - Impostando il tag live su "version 1", i canali che usano il bot useranno il tag "version 1".
    - Le versioni con tag delle applicazioni non sono interessate dalle modifiche, come la modifica di azioni ed entità e l'aggiunta di dialoghi di training.  
    - Le modifiche a un'applicazione, come la modifica di azioni ed entità e l'aggiunta di dialoghi di training, vengono sempre effettuate sul tag "master".  In altri termini, il tag "master" è l'unico che può essere modificato. Gli altri tag sono snapshot fissi.
    - I dialoghi di log nell'interfaccia utente di Conversation Learner usano sempre il master, non il tag live.

![](../media/tutorial16_v1_create.PNG)

Si noti la creazione della versione nelle impostazioni:

![](../media/tutorial16_settings.PNG)

Aggiungere una seconda azione:

1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione).
2. In Response (Risposta) immettere "bye bye (version 2)".

Modificare la prima azione:

1. Fare clic su Actions (Azioni).
2. In Actions (Azioni) fare clic su "hi there (version 1)".
3. Modificare la risposta in "hi there (version 2)".

![](../media/tutorial16_hi_there_v2.PNG)

### <a name="switch-to-the-bot-emulator"></a>Passare all'emulatore del bot

1. Nell'interfaccia utente del bot immettere "goodbye".
2. Si noti che il bot risponde con "hi there (version 1)".
    - Ciò indica che la versione 1 è "live". 

![](../media/tutorial16_bf_response.PNG)

### <a name="switch-to-the-web-ui"></a>Passare all'interfaccia utente Web

1. Fare clic su Log Dialogs (Dialoghi di log). Se non viene visualizzato alcun dialogo, aggiornare l'app.
2. Fare clic su "hi there (version 2)".

Si noti che è possibile apportare correzioni scegliendo tra tutte le azioni attualmente disponibili. Queste modifiche verranno applicate al master.

È stato illustrato il funzionamento del controllo delle versioni, nonché come interagire con il bot usando Bot Framework Emulator.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Demo: Reimpostazione della password](./demo-password-reset.md)
