---
title: Come usare i callback di sessione con un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare i callback di sessione con un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 73fb2a3f10476550bbe6bd20c387a1cc2a7727d8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51246481"
---
# <a name="how-to-use-session-callbacks-with-a-conversation-learner-model"></a>Come usare i callback di sessione con un modello di Conversation Learner

Questa esercitazione illustra i callback onSessionStart e onSessionEnd.

## <a name="video"></a>Video

[![Anteprima esercitazione 11](https://aka.ms/cl-tutorial-11-preview)](https://aka.ms/blis-tutorial-11)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot `tutorialSessionCallbacks` sia in esecuzione.

    npm run tutorial-session-callbacks

## <a name="details"></a>Dettagli
Questa esercitazione illustra il concetto di sessione, il modo in cui le sessioni vengono gestite per impostazione predefinita e come è possibile eseguire l'override di tale comportamento.

Una sessione è una conversazione con il bot. Può includere più turni, ma non lunghe pause nella conversazione, come ad esempio pause di 30 minuti.  Per informazioni sulla durata del timeout di sessione predefinito, vedere la pagina della guida sui limiti.

In caso di lunghe pause, il bot passerà alla sessione successiva.  All'avvio di una nuova sessione, la rete neurale ricorrente passa allo stato iniziale.  Per impostazione predefinita vengono cancellati anche tutti i valori di entità, ma questo comportamento può essere modificato (come illustrato di seguito).

### <a name="open-the-demo"></a>Aprire la demo

Nell'elenco dei modelli fare clic su Tutorial-11-SessionCallbacks. 

### <a name="entities"></a>Entità

Nel modello vengono definite quattro entità.

![](../media/tutorial11_entities.PNG)

Un aspetto da rilevare è che BotName è un'entità a livello di codice,  che verrà impostata dal bot all'avvio della sessione.

### <a name="actions"></a>Azioni

Nel modello vengono definite quattro azioni.

![](../media/tutorial11_actions.PNG)

Per prima cosa, questa esercitazione illustrerà come controllare i valori delle entità all'avvio della sessione, ad esempio impostando l'entità BotName prima di qualsiasi input dell'utente.

In secondo luogo, l'esercitazione dimostrerà come rendere persistenti i valori da una sessione alla successiva.  In questa esercitazione si presuppone che il nome e il numero di telefono dell'utente rimangano invariati da una sessione alla successiva, ma che la sua posizione possa cambiare.  Di conseguenza, si rendono persistenti il nome e il numero di telefono tra le sessioni, ma si cancella la posizione dell'utente.

### <a name="train-dialog"></a>Dialogo di training

Di seguito è riportato un dialogo di esempio. È un'unica sessione, senza lunghe pause nel dialogo.

![](../media/tutorial11_traindialog.PNG)

### <a name="code-for-the-callbacks"></a>Codice per i callback

Il codice per i metodi di callback si trova nel file c:\<percorsoinstallazione>\src\demos\tutorialSessionCallbacks.ts.

![](../media/tutorial11_code.PNG)

Entrambi questi metodi sono facoltativi.

- OnSessionStartCallback: questo metodo imposta l'entità BotName.
- OnSessionEndCallback: è possibile specificare ciò che si vuole conservare. In questo caso verranno cancellate tutte le entità tranne il nome e il numero di telefono dell'utente.

### <a name="try-the-bot"></a>Provare il bot

Passare all'interfaccia utente Web e fare clic su Log Dialogs (Dialoghi di log).

1. Immettere "hello".
2. Sistema: "Hi, I'm Botty. What's your name?". Il nome "Botty" proviene da OnSessionStartCallback.
3. Immettere "jason".
4. Sistema: "Hi jason. What's your phone number?".
5. Immettere "555-555-5555".
6. Sistema: "Can you tell Botty your location, jason?".
7. Digitare "Redmond".

Questa è una sessione. Per avviarne una nuova, è necessario terminare questa sessione. 

1. Fare clic su Session Timeout (Timeout sessione). Si passerà così alla sessione successiva.
    - Il pulsante "Session Timeout" (Timeout sessione) è disponibile a scopo di debug.  In una sessione effettiva deve verificarsi una lunga pausa, di circa 30 minuti.  Per informazioni sulla durata del timeout di sessione predefinito, vedere la pagina della guida sui limiti.
1. Immettere "hi".
2. Sistema: "Can you tell Botty your location, jason?".
    - Il sistema ha memorizzato il nome e il numero di telefono.
2. Immettere una nuova posizione, ossia "Seattle".
3. Sistema: "So, jason you are in Seattle".
4. Fare clic su Done Testing (Test completato).

Tornare a Log Dialogs (Dialoghi di log). Si noti che l'ultima conversazione è stata divisa in due parti, perché ogni dialogo di log corrisponde a una sessione.  

![](../media/tutorial11_splitdialogs.PNG)

- Nella prima interazione viene impostato Botty, ma nome e numero di telefono non sono impostati.
- La seconda interazione mostra il nome e il numero di telefono.

È stato illustrato come vengono gestite le sessioni per impostazione predefinita e come è possibile eseguire l'override del comportamento predefinito. 

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Chiamate API](./12-api-calls.md)
