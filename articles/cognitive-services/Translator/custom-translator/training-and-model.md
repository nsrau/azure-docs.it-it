---
title: Cos'è training e modello? - Custom Translator
titleSuffix: Azure Cognitive Services
description: 'Un modello è il sistema, che fornisce una traduzione per una coppia di lingue specifiche. Il risultato di un training riuscito è un modello. Durante il training di un modello, sono necessari tre set di dati che si escludono a vicenda: set di dati di training, set di dati di ottimizzazione e set di dati di test.'
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/17/2020
ms.author: swmachan
ms.openlocfilehash: d8713d20503dc788175a408f5ec652505090396e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88510828"
---
# <a name="what-are-trainings-and-models"></a>Cosa sono i training e i modelli?

Un modello è il sistema, che fornisce una traduzione per una coppia di lingue specifiche.
Il risultato di un training riuscito è un modello. Quando si esegue il training di un modello, sono necessari tre tipi di documento che si escludono a vicenda: training, ottimizzazione e test. È anche possibile specificare il tipo di documento del dizionario. Vedere allineamento delle [frasi](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment#suggested-minimum-number-of-sentences).

Se vengono forniti solo i dati di training durante l'accodamento di un training, il convertitore personalizzato assembla automaticamente i dati di ottimizzazione e test. Utilizzerà un subset casuale di frasi dei documenti di training ed escluderà tali frasi dai dati di training.

## <a name="training-document-type-for-custom-translator"></a>Tipo di documento di training per il convertitore personalizzato

I documenti inclusi nel set di training vengono utilizzati da Custom Translator come base per la compilazione del modello. Durante l'esecuzione del training, le frasi presenti in questi documenti vengono allineate (o abbinate). È possibile comporre liberamente i set di documenti di training. È possibile includere in un unico modello i documenti che si ritiene siano di rilevanza tangenziale. Anche in questo caso escluderli in un altro per visualizzare l'impatto in [punteggio BLEU (Bilingual Evaluation Understudy)](what-is-bleu-score.md). Purché si mantengono costanti il set di ottimizzazione e di test, è possibile sperimentare la composizione del set di training. Questo approccio è un modo efficace per modificare la qualità del sistema di traduzione.

È possibile eseguire più training all'interno di un progetto e confrontare i [punteggi BLEU](what-is-bleu-score.md) in tutte le esecuzioni di training. Quando si eseguono più training per il confronto, assicurarsi che siano specificati gli stessi set di dati di ottimizzazione / test ogni volta. Assicurarsi anche di ispezionare manualmente anche i risultati nella scheda ["Test"](how-to-view-system-test-results.md).

## <a name="tuning-document-type-for-custom-translator"></a>Tipo di documento di ottimizzazione per il convertitore personalizzato

I documenti paralleli inclusi in questo set sono utilizzati da Custom Traslator per regolare il sistema di traduzione per ottenere risultati ottimali.

I dati di ottimizzazione vengono usati durante il training per regolare tutti i parametri e i pesi del sistema di conversione ai valori ottimali. Scegliere con attenzione i dati di ottimizzazione: i dati di ottimizzazione devono essere rappresentativi del contenuto dei documenti che si intende tradurre in futuro. I dati di ottimizzazione hanno un impatto significativo sulla qualità delle traduzioni prodotte. L'ottimizzazione consente al sistema di conversione di fornire le traduzioni più vicine agli esempi forniti nei dati di ottimizzazione. Non sono necessarie più di 2500 frasi nei dati di ottimizzazione. Per una qualità di traduzione ottimale, si consiglia di selezionare manualmente il set di ottimizzazione scegliendo la selezione di frasi più rappresentativa.

Durante la creazione del set di ottimizzazione, scegliere frasi che hanno una lunghezza significativa e rappresentativa delle frasi che si prevede di tradurre in futuro. Si dovrebbero anche scegliere frasi che hanno parole ed espressioni che si intende tradurre nella distribuzione approssimativa delle traduzioni future. In pratica, la lunghezza di una frase da 7 a 10 parole produrrà i risultati migliori, perché queste frasi contengono un contesto sufficiente per mostrare la flessione e fornire una lunghezza di frase significativa, senza essere eccessivamente complesse.

Una buona descrizione del tipo di frasi da utilizzare nel set di ottimizzazione è la prosa: vere e proprie frasi scorrevoli. Non celle di tabella, poesie, liste di cose, non solo punteggiatura o numeri in una frase, ma linguaggio abituale.

Se si selezionano manualmente i dati di ottimizzazione, non dovrebbe avere le stesse frasi dei dati di training e di testing. I dati di ottimizzazione hanno un impatto significativo sulla qualità delle traduzioni. scegliere le frasi con cautela.

Se non si è certi di cosa scegliere per i dati di ottimizzazione, è sufficiente selezionare i dati di training e fare in modo che il traduttore personalizzato selezioni i dati di ottimizzazione. Quando si lascia che il convertitore personalizzato scelga automaticamente i dati di ottimizzazione, utilizzerà un subset casuale di frasi dei documenti di formazione bilingue ed escluderà tali frasi dal materiale di training.

## <a name="testing-dataset-for-custom-translator"></a>Set di dati di test per Custom Translator

I documenti paralleli inclusi nel set di test sono utilizzati per calcolare il punteggio BLEU (Bilingual Evaluation Understudy). Questo punteggio indica la qualità del sistema di traduzione. Questo punteggio indica in realtà quanto le traduzioni effettuate dal sistema di traduzione risultante da questo training corrispondono alle frasi di riferimento del set di dati del test.

Il punteggio BLEU è una misura del delta tra la traduzione automatica e la traduzione di riferimento. Il suo valore va da 0 a 100. Un punteggio pari a 0 indica che nemmeno una singola parola di riferimento viene visualizzata nella traduzione. Un punteggio pari a 100 indica che la traduzione automatica corrisponde esattamente al riferimento: la stessa parola si trova nella stessa esatta posizione. Il punteggio ricevuto è la media del Punteggio BLEU per tutte le frasi dei dati di test.

I dati di test devono includere documenti paralleli in cui le frasi della lingua di destinazione sono le traduzioni più desiderate delle frasi della lingua di origine corrispondenti nella coppia di destinazione di origine. Si consiglia di utilizzare gli stessi criteri utilizzati per comporre i dati di ottimizzazione. Tuttavia, i dati di test non hanno alcun effetto sulla qualità del sistema di traduzione. Viene usato esclusivamente per generare il Punteggio BLEU.

Non sono necessarie più di 2.500 frasi come dati di test. Se si consente al sistema di scegliere automaticamente il set di test, verrà utilizzato un subset casuale di frasi dai tuoi documenti di training bilingue escludendo quelle stesse frasi dal materiale di training.

È possibile visualizzare le traduzioni personalizzate del set di test e confrontarle con le traduzioni fornite nel set di test, passando alla scheda di test all'interno di un modello.
