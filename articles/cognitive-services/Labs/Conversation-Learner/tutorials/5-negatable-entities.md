---
title: Come usare le entità negabili con un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
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
ms.openlocfilehash: 2fd00d53755e44e3a3d86782c40aa6a53ff4d378
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171402"
---
# <a name="how-to-use-negatable-entities-with-a-conversation-learner-model"></a>Informazioni su come usare le entità negabili con un modello di Conversation Learner

Questa esercitazione illustra la proprietà "Negatable" (Negabile) delle entità.

## <a name="video"></a>Video

[![Anteprima esercitazione 5](http://aka.ms/cl-tutorial-05-preview)](http://aka.ms/blis-tutorial-05)

## <a name="requirements"></a>Requisiti
Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli
Contrassegnare un'azione come negabile se l'utente può "cancellare" un valore di entità, come in "No, I do not want $entity" o "no, not $entity". Ad esempio, "no, I do not want to leave from Boston".

In concreto, se la proprietà "Negatable" (Negabile) di un'entità è impostata:

- Quando si etichettano le menzioni dell'entità, è possibile etichettare sia istanze normali (positive) sia istanze negative dell'entità
- LUIS apprende due modelli di entità: uno per le istanze positive e un secondo per le istanze negative
- Un'istanza negativa dell'entità ha l'effetto di cancellare tale valore dalla variabile dell'entità, se esistente

## <a name="steps"></a>Passaggi

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su New Model (Nuovo modello)
2. In Name (Nome) immettere "NegatableEntity". Fare quindi clic su Create (Crea).

### <a name="create-an-entity"></a>Creare un'entità

1. Fare clic su Entities (Entità) e quindi su New Entity (Nuova entità).
2. In Entity Name (Nome entità) immettere "name".
3. Selezionare Negatable (Negabile).
    - Questa proprietà indica che l'utente potrà specificare un valore dell'entità oppure dichiarare che qualcosa *non* è il valore dell'entità. Questo secondo caso determinerà l'eliminazione di un valore corrispondente dell'entità.
3. Fare clic su Crea.

![](../media/tutorial5_entities.PNG)

### <a name="create-two-actions"></a>Creare due azioni

1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione).
2. In Response (Risposta) digitare "I don't know your name".
3. In Disqualifying Entities (Entità non qualificanti) immettere "name".
3. Click Create

Creare quindi la seconda azione.

1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione) per creare una seconda azione.
3. In Response (Risposta) digitare "I know your name. It is $name".
4. Click Create

Sono ora disponibili due azioni.

![](../media/tutorial5_actions.PNG)

### <a name="train-the-bot"></a>Eseguire il training del bot

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
2. Digitare "hello".
3. Fare clic su Score Actions (Punteggio azioni) e selezionare "I don't know your name".
    - Il punteggio è 100% perché è l'unica azione valida.
2. Immettere "my name is david".
3. Selezionare "david" e scegliere l'etichetta "+name".
    - Sono presenti due istanze di "name": "+name" e "-name".  Il segno Più (+) a aggiunge o sovrascrive il valore. Il segno Meno (-) rimuove il valore.
5. Fare clic su Score Actions (Punteggio azioni).
    - Il valore di name è ora presente nella memoria del bot.
    - "I know your name. It is $name" è l'unica risposta disponibile. 
6. Selezionare "I know your name. It is $name".

Provare a cancellare l'entità negabile:

7. Immettere "my name is not david".
    - Si noti che "not" viene selezionato come nome in base al modello precedente. Questa etichetta non è corretta.
2. Fare clic su "not" e quindi sulla X rossa. 
3. Fare clic su "david".
    - Questa è ora un'entità negativa che comunica che questo non è il valore dell'entità name.
2. Selezionare "-name".
3. Fare clic su Score Actions (Punteggio azioni).
    - Si noti che il valore è stato cancellato dalla memoria.
2. Selezionare "I don't know your name", che è l'unica azione disponibile.

Verrà ora illustrato come è possibile immettere un nuovo valore per name.

3. Immettere "john" come nome. Selezionare quindi "john" e fare clic su "name".
4. Fare clic su Score Actions (Punteggio azioni).
5. Selezionare "I know your name. It is $name".

Provare ora a sostituire il nome immesso.

6. Immettere "my name is susan".
7. Selezionare "I know your name. It is $name".
7. Fare clic su Score Actions (Punteggio azioni).
8. Si noti che **susan** ha sovrascritto **john** nei valori dell'entità.
9. Immettere "my name is not susan".
    - Si noti che questa frase è stata etichettata dal sistema come istanza negativa.
2. Fare clic su Score Actions (Punteggio azioni).
3. Selezionare "I don't know your name", che è l'unica azione disponibile.
7. Fare clic su Done Teaching (Training completato).

![](../media/tutorial5_dialogs.PNG)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità multivalore](./6-multi-value-entities.md)
