---
title: Informazioni sul punteggio di stima restituito da LUIS - Azure | Microsoft Docs
description: Informazioni sul significato del punteggio di stima in LUIS
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 31c101a23892df8599b8cdc0f67647fefb969490
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265989"
---
# <a name="prediction-score"></a>Punteggio di stima
Il punteggio di stima indica il grado di attendibilità rispetto ai risultati di stima di LUIS. 

Un punteggio di stima è in genere compreso tra zero (0) e uno (1). Un esempio di punteggio LUIS altamente attendibile è 0,99. Un esempio di punteggio poco attendibile è 0,01. 

|Valore punteggio|Attendibilità|
|--|--|
|1|corrispondenza definita|
|0,99|attendibilità elevata|
|0.01|attendibilità bassa|
|0|nessuna corrispondenza|

Quando un'espressione ottiene un punteggio di bassa attendibilità, LUIS lo evidenzia nella pagina [Intent][LUIS] (Finalità) del sito Web **LUIS**, con la **finalità etichettata** sottolineata in rosso. 

![Discrepanza nel punteggio](./media/luis-concept-score/score-discrepancy.png)

## <a name="top-scoring-intent"></a>Finalità con punteggio elevato
La stima di ogni espressione restituisce una finalità con punteggio elevato. Si tratta di un confronto numerico tra punteggi di stima. Tra i due punteggi più elevati può esserci una piccola differenza. LUIS non indica questa prossimità se non restituendo i punteggi.  

Se si è interessati alla prossimità dei punteggi più elevati, è necessario restituire il punteggio di tutte le finalità. È possibile aggiungere espressioni alle due finalità che indicano le relative differenze con la disposizione e la scelta delle parole oppure lasciare che l'applicazione chiamante LUIS, ad esempio un chatbot, effettui scelte programmatiche sulla modalità di gestione delle due finalità principali. 

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

## <a name="next-steps"></a>Passaggi successivi

Vedere [Aggiungere entità](luis-how-to-add-entities.md) per ulteriori informazioni sull'aggiunta di entità all'app LUIS.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions