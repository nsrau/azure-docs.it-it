---
title: Punteggi di stima
titleSuffix: Language Understanding - Azure Cognitive Services
description: Il punteggio di stima indica il grado di attendibilità rispetto ai risultati di stima di LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: diberry
ms.openlocfilehash: 9386285ae7c8692e16e8c476ad59f119ecb149b6
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870861"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>I punteggi di stima indicano l'accuratezza delle stime per la finalità e le entità
Il punteggio di stima indica il grado di attendibilità rispetto ai risultati di stima di LUIS. 

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
La stima di ogni espressione restituisce una finalità con punteggio elevato. Si tratta di un confronto numerico tra punteggi di stima. Tra i due punteggi più elevati può esserci una piccola differenza. LUIS non indica questa prossimità se non restituendo i punteggi.  

Se si è interessati alla prossimità dei punteggi più elevati, è necessario restituire il punteggio di tutte le finalità. È possibile aggiungere espressioni alle due finalità che indicano le relative differenze con la disposizione e la scelta delle parole oppure lasciare che l'applicazione chiamante LUIS, ad esempio un chatbot, effettui scelte programmatiche sulla modalità di gestione delle due finalità principali. 

Due finalità con punteggio troppo simile possono essere invertite a causa del training non deterministico. Il punteggio superiore può diventare il secondo punteggio mentre il secondo può diventare il primo. Per evitare questo problema, aggiungere espressioni di esempio a ognuna delle due finalità superiori per l'espressione con una scelta di parole e un contesto che le differenzino. Le due finalità devono avere circa lo stesso numero di espressioni di esempio. Una regola generale per la separazione per impedire l'inversione a causa del training è una differenza del 15% nei punteggi.

## <a name="return-prediction-score-for-all-intents"></a>Restituire un punteggio di stima per tutte le finalità
Un risultato di test o endpoint può includere tutte le finalità. Questa configurazione è impostata sull'[endpoint](https://aka.ms/v1-endpoint-api-docs) con la coppia nome/valore della stringa di query `verbose=true`. 

## <a name="review-intents-with-similar-scores"></a>Rivedere le finalità con punteggi simili
Rivedere il punteggio di tutte le finalità per verificare non solo che sia stata identificata la finalità corretta, ma che il punteggio della finalità identificata successivamente sia di molto inferiore in modo coerente per le espressioni. 

Se più finalità presentano punteggi di stima vicini, in base al contesto di un'espressione, LUIS potrebbe passare da una finalità a un'altra. Per risolvere questo problema, continuare ad aggiungere espressioni a ogni finalità con una varietà più ampia di differenze contestuali.   

## <a name="e-exponent-notation"></a>Notazione E (esponenziale)

I punteggi di stima possono usare la notazione esponenziale, dove *appare* sopra l'intervallo 0-1, ad esempio `9.910309E-07`. Questo punteggio è un'indicazione di un numero molto **piccolo**.

|Punteggio notazione esponenziale |Punteggio effettivo|
|--|--|
|9,910309E-07|0,0000009910309|

## <a name="differences-with-predictions"></a>Differenze con le stime
Quando si esegue il training dello stesso modello in un'altra app e i punteggi differiscono, nel training è insito un elemento di casualità. In secondo luogo, eventuali sovrapposizioni di un'espressione a più di una finalità indicano che la finalità principale della stessa espressione può cambiare in base al training.

Se il chatbot richiede un punteggio LUIS specifico per indicare l'attentibilità in una finalità, è consigliabile usare la differenza di punteggio tra le due finalità principali. Questo conferisce flessibilità in caso di variazioni nel training. 

## <a name="punctuation"></a>Punteggiatura
Punteggiatura è un token separato in LUIS. Un'espressione contenente un punto alla fine e un'espressione che non lo contiene sono due espressioni separate e possono ottenere due stime diverse. Verificare che il modello gestisca la punteggiatura nelle [espressioni di esempio](luis-concept-utterance.md) (con e senza punteggiatura) o nei [modelli](luis-concept-patterns.md) dove risulta più semplice ignorare la punteggiatura con la sintassi speciale: `I am applying for the {Job} position[.]`

## <a name="next-steps"></a>Passaggi successivi

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per ulteriori informazioni sull'aggiunta di entità all'app LUIS.
