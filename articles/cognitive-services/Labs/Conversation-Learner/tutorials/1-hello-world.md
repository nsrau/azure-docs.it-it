---
title: Come creare un modello "Hello World" di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come creare un modello "Hello World" di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 70b8f25bd699cbdb069892d65bf766ef3953f59d
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39170872"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Come creare un modello "Hello World" con Conversation Learner

Questa esercitazione illustra come iniziare a usare Conversation Learner, ad esempio creando azioni, eseguendo il training interattivo e apportando correzioni ai dialoghi registrati degli utenti finali.

## <a name="video"></a>Video

[![Anteprima esercitazione 1](http://aka.ms/cl-tutorial-01-preview)](http://aka.ms/blis-tutorial-01)


## <a name="requirements"></a>Requisiti
Se non si è ancora provveduto, per prima cosa assicurarsi che siano stati completati tutti i passaggi di configurazione, inclusa la creazione di un file con estensione `.env` con la chiave di creazione di LUIS.  Per informazioni dettagliate, vedere la [guida introduttiva](https://github.com/Microsoft/ConversationLearner-Samples).

Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="steps"></a>Passaggi

Per iniziare, passare alla home page nell'interfaccia utente Web.

### <a name="create-the-model"></a>Creare il modello
1. Fare clic su New Model (Nuovo modello)
2. Nel campo Name (Nome) immettere "Hello World"
3. Click Create

### <a name="create-an-action"></a>Creare un'azione

1. Fare clic sul modello Hello World per avviarlo
2. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione).
    - Un'azione può essere un messaggio di testo restituito da Conversation Learner all'utente, una chiamata API o una scheda.
3. In Response (Risposta) digitare "Hello World!".
    - Questa è la risposta che verrà restituita dal bot.
4. Click Create

È stata creata la prima operazione che può essere eseguita dal bot, costituita dalla restituzione di una risposta di testo.

### <a name="train-the-bot"></a>Eseguire il training del bot

#### <a name="create-the-first-dialog"></a>Creare il primo dialogo

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
2. Immettere un esempio dell'input dell'utente per iniziare la conversazione, ad esempio "hello".
3. Fare clic su Score Actions (Punteggio azioni).
4. Selezionare "Hello World!".
    - Viene così creato un dialogo di esempio di un turno. 
2. Immettere "goodbye".
3. Fare clic su Score Actions (Punteggio azioni).
4. Fare clic su Add Action (Aggiungi azione), quindi immettere "Goodbye!" in Response (Risposta) e infine fare clic su Create (Crea).
5. Fare clic su Done Teaching (Training completato). Verrà così terminato il dialogo di training.

Nel sistema è ora presente un dialogo di training.

#### <a name="continue-teaching-the-bot"></a>Continuare il training del bot
Eseguire un altro training e osservare la risposta del bot.

1. Fare clic su New Train Dialog (Nuovo dialogo di training).
2. Immettere "hi there".
    - Questa frase è simile al primo dialogo ed è quindi prevedibile un punteggio elevato dal bot.
2. Fare clic su Score Action (Punteggio azione).
    - La posizione e il punteggio potrebbero comunque non essere sufficientemente accurati e potrebbe essere necessario training aggiuntivo.
3. Fare clic su Select (Seleziona) accanto a "Hello World!".
4. Immettere quindi "bye".
5. Fare clic su Score Actions (Punteggio azioni).
6. Selezionare "Goodbye!".
7. Fare clic su Done Teaching (Training completato).

![](../media/tutorial1_actions.PNG)

Si eseguirà un'altra sessione di training per verificare il funzionamento del bot.

Ripetere i passaggi precedenti usando "hi" e "byebye" e rilevare i cambiamenti nella posizione e nel punteggio della risposta del bot quando si fa clic su Score Action (Punteggio azione).

È ora possibile ripetere i passaggi usando "howdy" e "good bye" e rilevare i miglioramenti nei punteggi, che indicano che il bot ha appreso questa interazione.

![](../media/tutorial1_dialogs.PNG)

### <a name="test-the-bot-as-an-end-user"></a>Testare il bot come utente finale

1. Fare clic su Log Dialogs (Dialoghi di log) e quindi su New Log Dialog (Nuovo dialogo di log).
2. Digitare "hello there".
3. Digitare quindi "bye".

È anche possibile provare a iniziare una conversazione con "bye" e osservare la risposta del bot.

### <a name="view-conversations-in-the-log-dialogs"></a>Visualizzare le conversazioni nei dialoghi di log

In Log Dialogs (Dialoghi di log) è possibile visualizzare l'elenco delle conversazioni, eseguire aggiornamenti e salvare le interazioni come dialoghi di training. A tale scopo, eseguire queste operazioni:

1. Fare clic sul log di una conversazione.
2. Se la conversazione risulta corretta, fare clic sull'ultima azione, ad esempio "Goodbye".
3. Fare clic per selezionare la risposta suggerita. 
    - È anche possibile selezionare o aggiungere un'altra azione.
4. Fare quindi clic su Done (Fine) per salvare la conversazione come dialogo di training.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Azioni con attesa e senza attesa](./2-wait-vs-nonwait-actions.md)