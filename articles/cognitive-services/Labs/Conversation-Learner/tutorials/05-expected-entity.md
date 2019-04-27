---
title: Come usare la proprietà "entità prevista" delle azioni di Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare la proprietà "entità prevista" in un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 01d991cff9b7f7a66740f86e537833ffe4e862c7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60707917"
---
# <a name="how-to-use-the-expected-entity-property-of-actions"></a>Come usare la proprietà "entità prevista" delle azioni

Questa esercitazione illustra la proprietà "entità prevista" delle azioni.

## <a name="video"></a>Video

[![Anteprima dell'esercitazione sull'entità prevista](https://aka.ms/cl_Tutorial_v3_ExpectedEntity_Preview)](https://aka.ms/cl_Tutorial_v3_ExpectedEntity)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli
Usare la proprietà "entità prevista" di un'azione per salvare la risposta dell'utente per questa azione in un'entità.

Quando si aggiungono entità alla proprietà "entità prevista" di un'azione, il sistema:

1. Inizierà a tentare di associare le entità usando il modello di estrazione di entità basato sull'apprendimento automatico.
2. Assegnerà l'intera espressione dell'utente a $entity in base all'euristica se non vengono trovate entità.
3. Chiamerà `EntityDetectionCallback` e procederà alla selezione dell'azione.

## <a name="steps"></a>Passaggi

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su "New Model" (Nuovo modello).
2. Nel campo "Name" (Nome) digitare "ExpectedEntities" e premere INVIO.
3. Fare clic sul pulsante "Create" (Crea).

### <a name="entity-creation"></a>Creazione di entità

1. Nel riquadro a sinistra fare clic su "Entities" (Entità), quindi sul pulsante "New Entity" (Nuova entità).
2. Selezionare "Custom Trained" (Con training personalizzato) per "Entity Type" (Tipo di entità).
3. Digitare "name" per "Entity Name" (Nome entità).
4. Fare clic sul pulsante "Create" (Crea).

> [!NOTE]
> Il tipo di entità con training personalizzato indica che questa entità può essere sottoposta a training, diversamente da altri tipi di entità.

![](../media/tutorial4_entities.PNG)

### <a name="create-the-first-action"></a>Creare la prima azione

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Nel campo "Bot's response..." (Risposta del bot...) digitare "What's your name?".
3. Nel campo "Expected Entities" (Entità previste) digitare "name".
4. Fare clic sul pulsante "Create" (Crea).

> [!NOTE]
> Le entità rilevate ed estratte dalla risposta dell'utente saranno salvate nell'entità "name" se si sceglie questa azione. Se non viene rilevata alcuna entità, verrà salvata l'intera risposta in questa entità.

### <a name="create-the-second-action"></a>Creare la seconda azione

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Nel campo "Bot's response..." (Risposta del bot...) digitare "Hi $name!".
3. Fare clic sul pulsante "Create" (Crea).

> [!NOTE]
> L'entità "name" (nome) è stata aggiunta automaticamente come entità obbligatoria dal riferimento nella risposta.

Sono ora disponibili due azioni.

![](../media/tutorial4_actions.PNG)

### <a name="train-the-model"></a>Eseguire il training del modello

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "hi".
    - In questo modo si simula il lato dell'utente della conversazione.
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "What's your name?".
    - La risposta "Hi $name!" non può essere selezionata in quanto questa risposta richiede che l'entità "name" sia definita ora nella memoria del modello.
5. Nel pannello della chat, dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "Frank".
    - "Frank" viene evidenziato come entità in base all'euristica impostata in precedenza per salvare la risposta come entità.
6. Fare clic su "Score Actions" (Punteggio azioni).
    - L'entità "name" viene ora definita come "Frank" nella memoria del modello e quindi è possibile selezionare "Hello $name" come azione.
7. Selezionare la risposta "Hi $name!".
8. Fare clic sul pulsante "Save" (Salva).

L'aggiunta di input alternativi esegue ulteriore training del modello.

1. Nel campo "Add alternative input..." (Aggiungi input alternativo...) digitare "I'm Jose".
    - Il modello non riconosce il nome come entità, quindi seleziona l'intero blocco di testo come valore dell'entità.
2. Fare clic sulla frase "I'm Jose", quindi scegliere l'icona del cestino.
3. Fare clic su "Jose", quindi fare clic su "name" nell'elenco di entità.
4. Fare clic su Score Actions (Punteggio azioni).
5. Selezionare la risposta "Hi Frank!".
6. Fare clic sul pulsante "Save" (Salva).

![](../media/tutorial4_dialogs.PNG)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità negabili](./06-negatable-entities.md)
