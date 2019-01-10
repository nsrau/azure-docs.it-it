---
title: Come usare le entità multivalore con un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare le entità multivalore con un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 2363dde7dc2462adde730fa9a4883ffb6c558f3f
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796538"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Come usare le entità multivalore con un modello di Conversation Learner
Questa esercitazione illustra la proprietà "multivalore" dell'entità.

## <a name="video"></a>Video

[![Anteprima dell'esercitazione sulle entità multivalore](https://aka.ms/cl_Tutorial_v3_MultiValued_Preview)](https://aka.ms/cl_Tutorial_v3_MultiValued)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli
Le entità multivalore accumulano i valori in un elenco, anziché archiviare un valore singolo.  Queste entità sono utili quando gli utenti possono specificare più valori. Ad esempio i condimenti su una pizza.

Per le entità contrassegnate come multivalore ogni istanza riconosciuta dell'entità sarà accodata a un elenco nella memoria del bot. I riconoscimenti successivi vengono accodati al valore dell'entità anziché sovrascritti.

## <a name="steps"></a>Passaggi

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su "New Model" (Nuovo modello).
2. Nel campo "Name" (Nome) digitare "MultiValueEntities" e premere INVIO.
3. Fare clic sul pulsante "Create" (Crea).

### <a name="entity-creation"></a>Creazione di entità

1. Nel riquadro a sinistra fare clic su "Entities" (Entità), quindi sul pulsante "New Entity" (Nuova entità).
2. Selezionare "Custom Trained" (Con training personalizzato) per "Entity Type" (Tipo di entità).
3. Digitare "toppings" per "Entity Name" (Nome dell'entità).
4. Selezionare la casella di controllo "Multi-valued" (Multivalore).
    - Le entità multivalore accumulano uno o più valori nell'entità.
5. Selezionare la casella di controllo "Negatable" (Negabile).
    - La proprietà "Negatable" è stata analizzata in un'altra esercitazione.
6. Fare clic sul pulsante "Create" (Crea).

![](../media/tutorial6_entities.PNG)

### <a name="create-the-first-action"></a>Creare la prima azione

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Nel campo "Bot's response..." (Risposta del bot...) digitare "Here are your toppings: $toppings"
    - Il segno di dollaro iniziale indica un riferimento a un'entità
3. Fare clic sul pulsante "Create" (Crea).

### <a name="create-the-second-action"></a>Creare la seconda azione

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Nel campo "Bot's Response..." (Risposta del bot...) digitare "What toppings would you like?"
3. Nel campo "Disqualifying Entities" (Entità non qualificanti) digitare "toppings".
4. Fare clic sul pulsante "Create" (Crea).

Sono ora disponibili due azioni.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-model"></a>Eseguire il training del modello

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "hi".
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "What pizza would you like?" (Che condimenti vuoi?)
    - Il percentile è 100%, in quanto unica azione valida in base ai vincoli.
5. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "cheese and mushrooms"
6. Fare clic su "cheese" (formaggio) e scegliere l'etichetta "+toppings"
7. Fare clic su "mushrooms" (funghi) e scegliere l'etichetta "+toppings"
8. Fare clic su "Score Actions" (Punteggio azioni).
9. Selezionare la risposta "Here are your toppings: $toppings" (Ecco i tuoi condimenti: $toppings)
10. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "add pepper"
11. Fare clic su "pepper" (peperoni) e scegliere l'etichetta "+toppings"
12. Fare clic su "Score Actions" (Punteggio azioni).
13. Selezionare la risposta "Here are your toppings: $toppings" (Ecco i tuoi condimenti: $toppings)
14. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "remove cheese"
15. Fare clic su "cheese" e scegliere l'etichetta "-toppings"
16. Fare clic su "Score Actions" (Punteggio azioni).
17. Selezionare la risposta "Here are your toppings: $toppings" (Ecco i tuoi condimenti: $toppings)

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità con training preliminare](./08-pre-trained-entities.md)
