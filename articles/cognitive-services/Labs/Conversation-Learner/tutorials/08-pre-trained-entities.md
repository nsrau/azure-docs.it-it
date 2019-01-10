---
title: Come aggiungere entità con training preliminare a un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come aggiungere entità con training preliminare a un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c198dfc19a350188f500af86c531be9a9ac424ce
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796544"
---
# <a name="how-to-add-pre-trained-entities"></a>Come aggiungere entità con training preliminare
Questa esercitazione illustra come aggiungere entità con training preliminare a un modello di Conversation Learner.

## <a name="video"></a>Video

[![Anteprima dell'esercitazione sulle entità con training preliminare](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities_Preview)](https://aka.ms/cl_Tutorial_v3_PreTrainedEntities)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli

Le entità con training preliminare riconoscono tipi comuni di entità, ad esempio numeri, date, importi monetari e altri tipi.  A differenza delle entità personalizzate, sono pronte da usare, non richiedono alcun tipo di training e il loro comportamento non può essere cambiato.  Per impostazione predefinita, le entità con training preliminare sono multivalore, vale a dire, accumulano ogni istanza identificata dell'entità.

## <a name="steps"></a>Passaggi

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su "New Model" (Nuovo modello).
2. Nel campo "Name" (Nome) digitare "PretrainedEntities" e premere INVIO.
3. Fare clic sul pulsante "Create" (Crea).

### <a name="entity-creation"></a>Creazione di entità

1. Nel riquadro a sinistra fare clic su "Entities" (Entità), quindi sul pulsante "New Entity" (Nuova entità).
2. Selezionare "Pre-Trained/datetimeV2" per "Entity Type" (Tipo di entità).
3. Selezionare la casella di controllo "Multi-valued" (Multivalore).
    - Le entità multivalore accumulano uno o più valori nell'entità.
    - Le proprietà negabili sono disabilitate per le entità con training preliminare.
4. Fare clic sul pulsante "Create" (Crea).

![](../media/tutorial7_entities_a.PNG)

### <a name="create-the-first-action"></a>Creare la prima azione

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Nel campo "Bot's response..." (Risposta del bot...) digitare "The date is $builtin-datetimev2".
3. Fare clic sul pulsante "Create" (Crea).

![](../media/tutorial7_actions_a.PNG)

### <a name="create-the-second-action"></a>Creare la seconda azione

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. NeI campo "Bot's response..." (Risposta del bot...) digitare "What's the date?".
    - Le entità con training preliminare non possono essere entità obbligatorie perché vengono riconosciute per impostazione predefinita per tutte le espressioni degli utenti.
3. Nel campo Disqualifying Entities (Entità non qualificanti) digitare "builtin-datetimev2".
4. Fare clic sul pulsante "Create" (Crea).

![](../media/tutorial7_actions2_a.PNG)

### <a name="train-the-model"></a>Eseguire il training del modello

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "hello".
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "What's the date?".
5. Nel pannello della chat, dove è indicato il messaggio "Type your message..." (Digita il tuo messaggio...) digitare "today".
    - L'espressione today viene riconosciuta automaticamente dai modelli con training preliminare di LUIS.
    - Posizionando il mouse sui valori delle entità con training preliminare vengono visualizzati dati aggiuntivi forniti dal servizio LUIS.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Resolver di entità](./09-entity-resolvers.md)
