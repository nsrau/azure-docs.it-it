---
title: Come usare le azioni con attesa e senza attesa con un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare le azioni con attesa e senza attesa con un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 95cf223004c50583701e65d3adf02f57bd541fae
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796586"
---
# <a name="wait-and-non-wait-actions"></a>Azioni con attesa e senza attesa

Questa esercitazione illustra la differenza tra azioni con attesa e senza attesa in Conversation Learner.

## <a name="video"></a>Video

[![Anteprima dell'esercitazione sulle azioni con attesa e senza attesa](https://aka.ms/cl_Tutorial_v3_WaitnonWait_Preview)](https://aka.ms/cl_Tutorial_v3_WaitnonWait)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli

- Azione con attesa: quando il sistema esegue un'azione "con attesa", interrompe l'esecuzione di azioni e attende l'input dell'utente.
- Azione senza attesa: quando il sistema esegue un'azione "senza attesa", sceglie immediatamente un'altra azione, senza attendere l'input dell'utente.

## <a name="steps"></a>Passaggi

### <a name="create-a-new-model"></a>Creare un nuovo modello

1. Nell'interfaccia utente Web fare clic su New Model (Nuovo modello)
2. Nel campo "Name" (Nome) digitare "Wait Non-Wait", premere INVIO o fare clic sul pulsante "Create" (Crea).

### <a name="create-the-first-two-wait-actions"></a>Creare le prime due azioni con attesa

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Nel campo "Bot's response..." (Risposta del bot...) digitare "What pizza would you like?".
    - Dato che questa è un'azione con attesa, lasciare selezionata la casella Wait for Response (Attendi risposta).
3. Fare clic sul pulsante "Create" (Crea).
4. Ripetere questi passaggi, creare un'altra azione con "Pizza on the way!" come risposta del Bot.

### <a name="train-using-those-wait-actions"></a>Eseguire il training usando queste azioni con attesa.

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "Hi". 
    - In questo modo si simula il lato dell'utente della conversazione.
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "What pizza would you like?".
5. Come utente, rispondere "Margherita".
6. Fare clic su "Score Actions" (Punteggio azioni).
7. Selezionare la risposta "Pizza on the way!".
8. Fare clic sul pulsante "Save" (Salva).

### <a name="create-a-non-wait-action-while-training"></a>Creare un'azione senza attesa durante il training
Anche se è possibile creare l'azione senza attesa come in precedenza, è possibile anche crearla all'interno di una sessione di training.
1. Fare clic sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Come utente, digitare "Hello".
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Fare clic sul pulsante "+ Action" (+ Azione). 
    - Verrà visualizzata la tipica finestra di dialogo "Create an Action" (Crea un'azione).
5. Digitare la risposta del bot come "Welcome to Pizza Bot!".
6. Deselezionare la casella di controllo "Wait for Response" (Attendi risposta).
7. Fare clic sul pulsante "Create" (Crea).
    - Si noti che il bot risponde immediatamente con "Welcome to Pizza Bot!" e che viene richiesta ancora un'altra risposta del bot perché la risposta del bot è stata l'azione senza attesa appena creata.
9. Selezionare la risposta "What pizza would you like?".
10. Come utente, rispondere "Margherita".
11. Fare clic su "Score Actions" (Punteggio azioni).
12. Selezionare la risposta "Pizza on the way!".
13. Fare clic sul pulsante "Save" (Salva).

> [!NOTE]
> La sequenza delle risposte del bot in relazione alle azioni con attesa e senza attesa.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Introduzione alle entità](./04-introduction-to-entities.md)
