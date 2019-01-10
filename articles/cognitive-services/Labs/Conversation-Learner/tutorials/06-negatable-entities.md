---
title: Come usare le entità negabili con un modello di Conversation Learner - Servizi cognitivi Microsoft | Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare le entità negabili con un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 34806803c50308a288007bf3bad129075ca8bc6d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796656"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Informazioni su come usare le entità negabili con un modello di Conversation Learner

Questa esercitazione illustra la proprietà "Negatable" (Negabile) delle entità.

## <a name="video"></a>Video

[![ Anteprima dell'esercitazione sulle entità negabili](https://aka.ms/cl_Tutorial_v3_NegatableEntities_Preview)](https://aka.ms/cl_Tutorial_v3_NegatableEntities)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli
La proprietà "Negatable" di un'entità consente di etichettare le istanze normali (positive) e negative dell'entità, eseguire il training in base ai modelli positivi e negativi e cancellare il valore di un'entità esistente. Le entità con il proprio set di proprietà "Negatable" sono chiamate entità negabili in Conversation Learner.

## <a name="steps"></a>Passaggi

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su "New Model" (Nuovo modello).
2. Nel campo "Name" (Nome), digitare "NegatableEntity" e premere INVIO.
3. Fare clic sul pulsante "Create" (Crea).

### <a name="entity-creation"></a>Creazione di entità

1. Nel riquadro a sinistra fare clic su "Entities" (Entità), quindi sul pulsante "New Entity" (Nuova entità).
2. Selezionare "Custom" (Personalizzata) per "Entity Type" (Tipo di entità).
3. Digitare "name" per "Entity Name" (Nome entità).
4. Selezionare la casella di controllo "Negatable" (Negabile).
    - Selezionando questa proprietà l'utente può specificare un valore di entità o dire qualcosa che *non* rappresenta un valore di entità. In questo ultimo caso, il risultato è l'eliminazione del valore di entità corrispondente.
5. Fare clic sul pulsante "Create" (Crea).

![](../media/tutorial5_entities.PNG)

### <a name="create-the-first-action"></a>Creare la prima azione

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Nel campo "Bot's response..." (Risposta del bot), digitare "I don't know your name".
3. Nel campo "Disqualifying Entities" (Entità non qualificanti) digitare "nome".
4. Fare clic sul pulsante "Create" (Crea).

### <a name="create-the-second-action"></a>Creare la seconda azione

1. Nel riquadro a sinistra fare clic su "Actions" (Azioni), quindi sul pulsante "New Action" (Nuova azione).
2. Nel campo "Bot's response..." (Risposta del bot), digitare "I know your name". It is $name".
3. Fare clic sul pulsante "Create" (Crea).

> [!NOTE]
> L'entità "name" (nome) è stata aggiunta automaticamente come entità obbligatoria dal riferimento nella risposta.

Sono ora disponibili due azioni.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-model"></a>Eseguire il training del modello

1. Nel riquadro a sinistra fare clic su "Train Dialogs" (Dialoghi di training), quindi sul pulsante "New Train Dialog" (Nuovo dialogo di training).
2. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "hello".
3. Fare clic su "Score Actions" (Punteggio azioni).
4. Selezionare la risposta "I don't know your name".
    - Il percentile è 100%, in quanto unica azione valida in base ai vincoli.
5. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "My name is Frank".
6. Selezionare "Frank" e scegliere l'etichetta "+name".
    - Sono presenti due istanze dell'entità "name": "+name" e "-name".  Il segno Più (+) a aggiunge o sovrascrive il valore. Il segno Meno (-) rimuove il valore.
7. Fare clic su "Score Actions" (Punteggio azioni).
    - L'entità "name" è ora definita come "Frank" nella memoria del modello, pertanto l'azione "I know your name. It is $name" è disponibile.
8. Selezionare la risposta "I know your name". It is $name".
9. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "My name is not Frank".
10. Selezionare "Frank" e scegliere l'etichetta "-name".
    - "-name" viene selezionato per cancellare il valore corrente dell'entità.
11. Fare clic su "Score Actions" (Punteggio azioni).
12. Selezionare la risposta "I don't know your name".
13. Nel pannello della chat dove è indicato "Type your message..." (Digita il tuo messaggio...) digitare "My name is Susan".
14. Selezionare "Susan" e scegliere l'etichetta "+name".

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità multivalore](./07-multi-value-entities.md)
