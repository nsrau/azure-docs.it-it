---
title: Punteggi di stima-LUIS
titleSuffix: Azure Cognitive Services
description: Un punteggio di stima indica il grado di attendibilità del servizio API LUIS per i risultati della stima, in base a un enunciato utente.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: b360bc82b80e834492b524acc5c4535b0409eda1
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74280816"
---
# <a name="prediction-scores-indicate-prediction-accuracy-for-intent-and-entities"></a>I punteggi di stima indicano l'accuratezza delle stime per la finalità e le entità

Un punteggio di stima indica il grado di confidenza LUIS per i risultati della stima di un enunciato utente.

Un punteggio di stima è compreso tra zero (0) e uno (1). Un esempio di punteggio LUIS altamente attendibile è 0,99. Un esempio di punteggio poco attendibile è 0,01. 

|Valore del punteggio|Attendibilità|
|--|--|
|1|corrispondenza definita|
|0,99|attendibilità elevata|
|0.01|attendibilità bassa|
|0|nessuna corrispondenza|

## <a name="top-scoring-intent"></a>Finalità con punteggio elevato

La stima di ogni espressione restituisce una finalità con punteggio elevato. Questa stima è un confronto numerico dei punteggi di stima. 

## <a name="proximity-of-scores-to-each-other"></a>Prossimità dei punteggi tra loro

I primi 2 punteggi possono avere una piccola differenza tra di essi. LUIS non indica questa prossimità oltre alla restituzione del punteggio superiore.  

## <a name="return-prediction-score-for-all-intents"></a>Restituire un punteggio di stima per tutte le finalità

Un risultato di test o endpoint può includere tutte le finalità. Questa configurazione viene impostata sull'endpoint utilizzando la coppia nome/valore QueryString corretta.

|Api di stima|Nome QueryString|
|--|--|
|V3|`show-all-intents=true`|
|V2|`verbose=true`|

## <a name="review-intents-with-similar-scores"></a>Rivedere le finalità con punteggi simili

La revisione del punteggio per tutti gli Intent è un buon metodo per verificare che non solo venga identificato lo scopo corretto, ma che il punteggio successivo della finalità identificata sia significativamente e costantemente inferiore per le espressioni.

Se più finalità presentano punteggi di stima vicini, in base al contesto di un'espressione, LUIS potrebbe passare da una finalità a un'altra. Per risolvere questo problema, continuare ad aggiungere espressioni a ogni finalità con una varietà più ampia di differenze contestuali oppure è possibile fare in modo che l'applicazione client, ad esempio un bot di chat, faccia scelte a livello di codice su come gestire i 2 Intent principali.

I 2 Intent, che hanno un punteggio troppo ravvicinato, possono essere invertiti a causa della **formazione non deterministica**. Il punteggio superiore può diventare il secondo punteggio mentre il secondo può diventare il primo. Per evitare questa situazione, aggiungere espressioni di esempio a ognuno dei primi due intenti per tale espressione con scelta e contesto di parola che differenziano i due intenti. Le due finalità devono avere circa lo stesso numero di espressioni di esempio. Una regola generale per la separazione per impedire l'inversione a causa del training è una differenza del 15% nei punteggi.

È possibile disattivare il **Training non deterministico** eseguendo il [training con tutti i dati](luis-how-to-train.md#train-with-all-data).

## <a name="differences-with-predictions-between-different-training-sessions"></a>Differenze con le stime tra sessioni di training diverse

Quando si esegue il training dello stesso modello in un'altra app e i punteggi non sono gli stessi, la differenza è dovuta al fatto che è presente un **Training non deterministico** (un elemento di casualità). In secondo luogo, eventuali sovrapposizioni di un'espressione a più di una finalità indicano che la finalità principale della stessa espressione può cambiare in base al training.

Se il bot di chat richiede un punteggio LUIS specifico per indicare la confidenza, è necessario usare la differenza di punteggio tra i primi due intenti. Questa situazione offre flessibilità per le variazioni nel training.

È possibile disattivare il **Training non deterministico** eseguendo il [training con tutti i dati](luis-how-to-train.md#train-with-all-data).

## <a name="e-exponent-notation"></a>Notazione E (esponenziale)

I punteggi di stima possono usare la notazione esponenziale, dove _appare_ sopra l'intervallo 0-1, ad esempio `9.910309E-07`. Questo punteggio è un'indicazione di un numero molto **piccolo**.

|Punteggio notazione esponenziale |Punteggio effettivo|
|--|--|
|9,910309E-07|0,0000009910309|

## <a name="punctuation"></a>Punteggiatura

[Altre](luis-concept-utterance.md#punctuation-marks) informazioni su come usare o ignorare la punteggiatura. 

## <a name="next-steps"></a>Passaggi successivi

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per ulteriori informazioni sull'aggiunta di entità all'app LUIS.
