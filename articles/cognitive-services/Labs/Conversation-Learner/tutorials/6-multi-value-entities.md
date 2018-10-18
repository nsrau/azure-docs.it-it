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
ms.openlocfilehash: a80577bb226cbec080edf5e06dbd0f31c80a5890
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321428"
---
# <a name="how-to-use-multi-value-entities-with-a-conversation-learner-model"></a>Come usare le entità multivalore con un modello di Conversation Learner
Questa esercitazione illustra la proprietà "multivalore" dell'entità.

## <a name="video"></a>Video

[![Anteprima esercitazione 6](http://aka.ms/cl-tutorial-06-preview)](http://aka.ms/blis-tutorial-06)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli
Un'entità "multivalore" accumula i valori in un elenco, piuttosto che archiviare un valore singolo.  Ciò è utile per le entità in cui l'utente può specificare più valori, ad esempio i condimenti per una pizza.

Concretamente, se un'entità è contrassegnata come "multivalore", ogni istanza riconosciuta dell'entità verrà aggiunta a un elenco nella memoria del bot (invece di sovrascrivere un singolo valore di entità).

## <a name="steps"></a>Passaggi

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su New Model (Nuovo modello)
2. In Nome immettere MultiValueEntities. Fare quindi clic su Crea.

### <a name="create-an-entity"></a>Creare un'entità

1. Fare clic su Entities (Entità) e quindi su New Entity (Nuova entità).
2. In Entity Name (Nome entità) immettere "Toppings".
3. Selezionare Multi-valued (Multivalore).
    - Le entità multivalore accumulano uno o più valori nell'entità.
2. Selezionare Negatable (Negabile).  
    - Ciò consente all'utente di rimuovere condimenti dal suo elenco di condimenti per pizza accumulati.
3. Fare clic su Crea.

![](../media/tutorial6_entities.PNG)

### <a name="create-two-actions"></a>Creare due azioni

1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione).
2. In Response (Risposta) digitare 'What toppings do you want?'.
3. In Disqualifying Entities (Entità non qualificanti) immettere "Toppings".
3. Fare clic su Crea

Creare quindi la seconda azione.

1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione) per creare una seconda azione.
3. In Response (Risposta) digitare 'Here are your toppings: $Toppings'.
4. Fare clic su Crea

Sono ora disponibili due azioni.

![](../media/tutorial6_actions.PNG)

### <a name="train-the-bot"></a>Eseguire il training del bot

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
2. Digitare "hello".
3. Fare clic su Score Actions (Punteggio azioni) e selezionare "What toppings do you want?".
2. Immettere 'mushrooms and cheese'. 
    - È possibile applicare un'etichetta a zero, una o più entità.
3. Fare clic su 'mushrooms' e selezionare Toppings.
4. Fare clic su 'cheese' e selezionare Toppings.
5. Fare clic su Score Actions (Punteggio azioni).
    - I due valori sono ora presenti nell'entità Toppings. 
6. Selezionare 'Here are your toppings: $Toppings'.

È possibile aggiungere altro:

7. Immettere 'add peppers'.
    - Fare clic su 'peppers' in Entity Detection (Rilevamento entità) e selezionare Toppings.
3. Fare clic su Score Actions (Punteggio azioni).
    - "peppers" viene ora visualizzato come valore aggiuntivo in Toppings.
6. Selezionare 'Here are your toppings: $Toppings'.

È ora possibile rimuovere un condimento e aggiungerne uno:

2. Digitare 'remove peppers and add sausage'.
1. Fare clic su 'peppers' e quindi sulla x rossa per rimuoverlo.
2. Fare clic su 'peppers' e selezionare '-Toppings'.
3. Fare clic su Score Actions (Punteggio azioni).
    - "peppers" è stato eliminato ed è stato aggiunto "sausage".
6. Selezionare 'Here are your toppings: $Toppings'.

Provare ora a rimuovere tutti i valori:

6. Immettere 'remove mushrooms, remove cheese, and remove sausage'.
7. Fare clic su ognuno dei tre e selezionare '-Toppings'.
7. Fare clic su Score Actions (Punteggio azioni).
    - Tutti i condimenti vengono cancellati.
2. Selezionare 'What toppings do you want?'
3. Fare clic su Done Teaching (Training completato).

![](../media/tutorial6_dialogs.PNG)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità predefinite](./7-built-in-entities.md)
