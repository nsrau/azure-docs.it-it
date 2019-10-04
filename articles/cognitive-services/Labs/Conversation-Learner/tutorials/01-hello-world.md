---
title: Come creare un modello "Hello World" di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come creare un modello "Hello World" di Conversation Learner.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: bb1d053af8813f05872c56d3b5609f2d7d7d5d8c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705632"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Come creare un modello "Hello World" con Conversation Learner

Questa esercitazione illustra come iniziare a usare Conversation Learner, ad esempio creando azioni, eseguendo il training interattivo del bot e apportando correzioni ai dialoghi registrati degli utenti finali.

## <a name="video"></a>Video

[![Anteprima dell'esercitazione su Hello World](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Requisiti
Se non si è ancora provveduto, per prima cosa assicurarsi che siano stati completati tutti i passaggi di configurazione, inclusa la creazione di un file con estensione `.env` con la chiave di creazione di LUIS.  Per informazioni dettagliate, vedere la [guida introduttiva](../quickstart.md).

Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione.

    npm run tutorial-general

## <a name="steps"></a>Passaggi

Per iniziare, passare alla home page nell'interfaccia utente Web.

### <a name="create-the-model"></a>Creare il modello
1. Fare clic sul pulsante "New Model" (Nuovo modello).
2. Nel campo Name (Nome) immettere "Hello World".
3. Fare clic sul pulsante "Create" (Crea).

A questo punto dovrebbe essere visualizzato il modello creato.

### <a name="create-an-action"></a>Creare un'azione
1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
    - Un'azione può essere un messaggio di testo restituito da Conversation Learner all'utente, una chiamata API o una scheda.
2. Nel campo "Bot's response..." (Risposta del bot...) digitare "Hello".
    - Questa è la risposta che verrà restituita dal bot.
3. Fare clic sul pulsante "Create" (Crea).

È stata creata la prima azione che il bot può eseguire, cioè restituire una risposta di testo.

### <a name="train-dialogs"></a>Dialoghi di training
Qui è possibile eseguire il training del modello su come rispondere alle espressioni degli utenti.

#### <a name="first-training-dialog"></a>Primo dialogo di training

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Digitare "Hi" e premere INVIO.
    - È un esempio di come un utente potrebbe iniziare una conversazione.
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare "Hello".
    - È stato appena completato un turno completo in questo dialogo di esempio. 
5. Digitare la risposta dell'utente "Goodbye".
6. Fare clic su "Score Actions" (Punteggio azioni).
7. Fare clic sul pulsante "+ Action" (+ Azione).
8. Digitare "Goodbye!" nel campo "Bot's response..." (Risposta del bot...) e quindi fare clic sul pulsante "Create" (Crea).
    - Si noti che il bot ha risposto usando l'azione appena creata.
9. Fare clic sul pulsante "Save" (Salva). 
    - A questo punto il dialogo di training è stato terminato e salvato.

Ora è disponibile un dialogo di training nel modello, insieme a una singola entità e a due azioni.

#### <a name="second-training-dialog"></a>Secondo dialogo di training
È possibile eseguire altro training e verificare come risponde il bot.

1. Fare clic sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Digitare "Hi".
    - Questo dialogo è simile al primo ed è prevedibile che venga assegnato un punteggio elevato dal bot.
3. Fare clic su "Score Actions" (Punteggio azioni).
    - La posizione e il punteggio potrebbero non essere ancora sufficientemente accurati e potrebbe essere necessario training aggiuntivo.
4. Selezionare "Hello".
5. Digitare la risposta dell'utente "bye".
6. Fare clic su "Score Actions" (Punteggio azioni).
7. Selezionare "Goodbye!".
8. Fare clic sul pulsante "Save" (Salva).

### <a name="log-dialogs"></a>Dialoghi di log
Questo è il punto in cui testare, visualizzare e correggere le conversazioni avute con il bot o che gli utenti del mondo reale hanno avuto con il bot.

#### <a name="test-the-model-as-an-end-user"></a>Testare il modello come utente finale
1. Nel riquadro a sinistra fare clic su "Log Dialogs" (Dialoghi di log), quindi sul pulsante "New Log Dialog" (Nuovo dialogo di log).
2. Digitare "hello there".
3. Attendere un breve intervallo di tempo, il bot dovrebbe rispondere automaticamente con "Hello".
4. Immettere "byebye".
5. Attendere un breve intervallo di tempo, il bot dovrebbe nuovamente rispondere automaticamente con "Hello".
6. Fare clic sul pulsante "Done Testing" (Test completato).

#### <a name="view-and-correct-a-user-conversation"></a>Visualizzare e correggere la conversazione di un utente
Usando i dialoghi di log è possibile visualizzare l'elenco delle conversazioni degli utenti con il bot. È possibile anche modificarle per correggere le risposte del bot e salvare le interazioni come dialoghi di training. A tale scopo, eseguire queste operazioni:
1. Nella griglia fare clic sul log della conversazione.
2. Fare clic sull'ultima azione del bot, ad esempio "Hello".
3. Selezionare "Goodbye!" per correggere il bot.
4. Fare clic sul pulsante "Save As Train Dialog" (Salva come dialogo di training).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione al training](./02-intro-to-training.md)
