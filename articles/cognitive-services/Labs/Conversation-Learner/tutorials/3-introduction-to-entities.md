---
title: Come usare le entità con un modello di Conversation Learner - Servizi cognitivi Microsoft| Microsoft Docs
titleSuffix: Azure
description: Informazioni su come usare le entità con un modello di Conversation Learner.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: f851d43d69999a848dea01c9457a379adb63353b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/20/2018
ms.locfileid: "39172382"
---
# <a name="introduction-to-entities"></a>Introduzione alle entità

Questa esercitazione illustra le entità e come usare i campi "Disqualifying entities" (Entità non qualificanti) e "Required entities" (Entità obbligatorie) nelle azioni.

## <a name="video"></a>Video

[![Anteprima esercitazione 3](http://aka.ms/cl-tutorial-03-preview)](http://aka.ms/blis-tutorial-03)

## <a name="requirements"></a>Requisiti

Per questa esercitazione è necessario che il bot di esercitazione generale sia in esecuzione

    npm run tutorial-general

## <a name="details"></a>Dettagli

Questa esercitazione illustra due usi comuni delle entità.  In primo luogo, le entità possono estrarre sottostringhe da un messaggio dell'utente, identificando ad esempio una città in "what's the weather in Seattle?".  In secondo luogo, possono limitare i casi in cui le azioni sono disponibili.  In particolare, un'entità può essere inclusa in un'azione come "obbligatoria" o "non qualificante":
- Le entità obbligatorie di un'azione devono essere presenti nella memoria del bot affinché l'azione sia disponibile
- Le entità non qualificanti *non* devono essere presenti nella memoria del bot affinché l'azione sia disponibile

Altri aspetti delle entità, come le entità predefinite, le entità negabili e multivalore, le entità a livello di codice e la modifica delle entità nel codice, vengono illustrati in altre esercitazioni.

## <a name="steps"></a>Passaggi

### <a name="create-the-model"></a>Creare il modello

1. Nell'interfaccia utente Web fare clic su New Model (Nuovo modello)
2. In Name (Nome) immettere "IntroToEntities". Fare quindi clic su Create (Crea).

### <a name="create-entity"></a>Crea un'entità

1. Fare clic su Entities (Entità) e quindi su New Entity (Nuova entità).
2. In Entity Name (Nome entità) immettere "city".
3. Click Create

> [!NOTE]
> Si tratta di un'entità di tipo "custom" (personalizzato) ed è quindi possibile eseguirne il training.  Sono disponibili anche entità predefinite il cui comportamento non può essere modificato. Queste entità sono illustrate in un'altra esercitazione.

### <a name="create-two-actions"></a>Creare due azioni

1. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione).
2. In Response (Risposta) digitare "I don't know what city you want".
3. In Disqualifying Entities (Entità non qualificanti) immettere "$city". Fare clic su Save.
    - In questo modo, se l'entità è definita nella memoria del bot, l'azione *non* verrà eseguita.
2. Fare clic su Actions (Azioni) e quindi su New Action (Nuova azione) per creare una seconda azione.
3. In Response (Risposta) digitare "The weather in the $city is probably sunny".
4. In Required Entities (Entità obbligatorie) l'entità city è stata aggiunta automaticamente perché vi viene fatto riferimento.
5. Fare clic su Salva.

Sono ora disponibili due azioni.

![](../media/tutorial3_actions.PNG)

### <a name="train-the-bot"></a>Eseguire il training del bot

1. Fare clic su Train Dialogs (Dialoghi di training) e quindi su New Train Dialog (Nuovo dialogo di training).
2. Digitare "hello".
3. Fare clic su Score Actions (Punteggio azioni) e selezionare "I don't know what city you want".
    - La risposta in cui l'entità city è obbligatoria non può essere selezionata perché l'entità city non è definita nella memoria del bot.
2. Selezionare "I don't know what city you want".
4. Immettere "seattle". Evidenziare "seattle" e quindi fare clic su "city".
5. Fare clic su Score Actions (Punteggio azioni).
    - Il valore di city è ora presente nella memoria del bot.
    - "Weather in $city is probably sunny" è ora disponibile come risposta. 
6. Selezionare "Weather in $city is probably sunny".

Si supponga che l'utente immetta "repeat that". 
1. Digitare e premere INVIO. L'entità city e il relativo valore sono presenti in memoria e disponibili.
2. Selezionare "Weather in $city is probably sunny".

![](../media/tutorial3_entities.PNG)

È stata creata un'entità e ne sono state etichettate le istanze nei messaggi dell'utente.  È stata inoltre usata la presenza/assenza dell'entità nella memoria del bot per controllare i casi in cui le azioni sono disponibili, tramite i campi delle entità obbligatorie e non qualificanti dell'azione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Entità prevista](./4-expected-entity.md)
