---
title: Punteggi di stima
titleSuffix: Language Understanding - Azure Cognitive Services
description: Un punteggio della stima indica il livello di confidenza è il servizio API LUIS per i risultati della stima, in base utterance un utente.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: diberry
ms.openlocfilehash: 383ce4c4248f7e21f745f503c74a29cb613983e2
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58121758"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>I punteggi di stima indicano l'accuratezza delle stime per la finalità e le entità

Un punteggio della stima indica il livello di confidenza LUIS ha per i risultati della stima, in base utterance un utente.

Un punteggio di stima è compreso tra zero (0) e uno (1). Un esempio di punteggio LUIS altamente attendibile è 0,99. Un esempio di punteggio poco attendibile è 0,01. 

|Valore punteggio|Attendibilità|
|--|--|
|1|corrispondenza definita|
|0,99|attendibilità elevata|
|0.01|attendibilità bassa|
|0|nessuna corrispondenza|

Quando un'espressione ottiene un punteggio di bassa attendibilità, LUIS lo evidenzia nella pagina [Intent](luis-reference-regions.md) (Finalità) del sito Web **LUIS**, con la **finalità etichettata** sottolineata in rosso.

![Discrepanza nel punteggio](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Finalità con punteggio elevato

La stima di ogni espressione restituisce una finalità con punteggio elevato. Questa stima è un confronto numerico dei punteggi di stima. I punteggi massimi 2 possono avere una piccola differenza tra di essi. LUIS non indica grazie a questa prossimità diverso da restituire il punteggio superiore.  

## <a name="return-prediction-score-for-all-intents"></a>Restituire un punteggio di stima per tutte le finalità

Un risultato di test o endpoint può includere tutte le finalità. Questa configurazione è impostata sull'[endpoint](https://aka.ms/v1-endpoint-api-docs) con la coppia nome/valore della stringa di query `verbose=true`.

## <a name="review-intents-with-similar-scores"></a>Rivedere le finalità con punteggi simili

Rivedere il punteggio di tutte le finalità per verificare non solo che sia stata identificata la finalità corretta, ma che il punteggio della finalità identificata successivamente sia di molto inferiore in modo coerente per le espressioni.

Se più finalità presentano punteggi di stima vicini, in base al contesto di un'espressione, LUIS potrebbe passare da una finalità a un'altra. Per risolvere questa situazione, continuare ad aggiungere espressioni a ogni scopo con un'ampia gamma di differenze contestuale oppure è possibile che l'applicazione client, ad esempio un chat bot, effettuare scelte a livello di codice su come gestire gli scopi principali 2.

Può invertire il 2 Intent, sono troppo a stretto contatto con punteggio, a causa di training non deterministica. Il punteggio superiore può diventare il secondo punteggio mentre il secondo può diventare il primo. Per evitare questa situazione, aggiungere espressioni di esempio per ognuno dei primi due finalità per tale utterance le opzioni di word e rapida che consentono di distinguere il 2 Intent. Le due finalità devono avere circa lo stesso numero di espressioni di esempio. Una regola generale per la separazione per impedire l'inversione a causa del training è una differenza del 15% nei punteggi.

È possibile disattivare il training non deterministica dal [con tutti i dati di training](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Differenze con stime tra le sessioni di training diversi

Quando si esegue il training lo stesso modello in un'altra app e i punteggi non sono uguali, questa differenza è perché si è a scopo formativo deterministica (un elemento di casualità). In secondo luogo, eventuali sovrapposizioni di un'espressione a più di una finalità indicano che la finalità principale della stessa espressione può cambiare in base al training.

Se il bot chat richiede un punteggio di LUIS specifico per indicare la fiducia in un intent, è consigliabile usare la differenza di punteggio tra i primi due finalità. Questa situazione offre flessibilità per le variazioni nel training.

## <a name="e-exponent-notation"></a>Notazione E (esponenziale)

I punteggi di stima possono usare la notazione esponenziale, dove *appare* sopra l'intervallo 0-1, ad esempio `9.910309E-07`. Questo punteggio è un'indicazione di un numero molto **piccolo**.

|Punteggio notazione esponenziale |Punteggio effettivo|
|--|--|
|9,910309E-07|0,0000009910309|

## <a name="punctuation"></a>Punteggiatura

Punteggiatura è un token separato in LUIS. Un utterance contenente un punto alla fine e un utterance che non contiene un punto alla fine sono due espressioni separate e possono ottenere due diverse stime. Verificare che il modello gestisca la punteggiatura nelle [espressioni di esempio](luis-concept-utterance.md) (con e senza punteggiatura) o nei [modelli](luis-concept-patterns.md) dove risulta più semplice ignorare la punteggiatura con la sintassi speciale: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>Passaggi successivi

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per ulteriori informazioni sull'aggiunta di entità all'app LUIS.
