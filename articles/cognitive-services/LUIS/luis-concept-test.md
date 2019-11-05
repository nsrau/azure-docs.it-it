---
title: Testare l'app LUIS
titleSuffix: Azure Cognitive Services
description: Il processo di test consiste nel fornire espressioni di esempio a LUIS e nell'ottenere una risposta di finalità ed entità riconosciute da LUIS.
author: diberry
manager: nitinme
services: cognitive-services
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: diberry
ms.openlocfilehash: 25b360f90a0920aad2ea5e68cda31a68be5d37a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73486688"
---
# <a name="testing-example-utterances-in-luis"></a>Test di espressioni di esempio in LUIS

Il processo di test consiste nel fornire espressioni di esempio a LUIS e nell'ottenere una risposta di finalità ed entità riconosciute da LUIS. 

È possibile testare LUIS in modo interattivo, un enunciato alla volta o fornire un oggetto di espressioni. Durante il test, è possibile confrontare la risposta di stima del modello attivo corrente con la risposta di stima del modello pubblicato. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>

## <a name="what-is-a-score-in-testing"></a>Cos'è il punteggio nell'ambito del test?
Vedere i concetti relativi al [punteggio di stima](luis-concept-prediction-score.md) per ulteriori informazioni sui punteggi di stima.

## <a name="interactive-testing"></a>Test interattivi
Il test interattivo viene eseguito dal pannello **test** del portale Luis. È possibile immettere un'espressione per vedere in che modo vengono identificate le finalità e le entità e quale punteggio viene attribuito loro. Se LUIS non sta stimando gli Intent e le entità come previsto in un enunciato nel pannello di test, copiarlo nella pagina **finalità** come nuova espressione. Quindi etichettare le parti di tale espressione per le entità e Train LUIS. 

## <a name="batch-testing"></a>Test in batch
Vedere [Test in batch](luis-concept-batch-test.md) se si testa più di un'espressione alla volta.

## <a name="endpoint-testing"></a>Test con endpoint
È possibile eseguire il test con l'[endpoint](luis-glossary.md#endpoint) con un massimo di due versioni dell'app. Con la versione principale o in tempo reale dell'app impostata come endpoint di **produzione**, aggiungere una seconda versione all'endpoint di **staging**. Questo approccio fornisce tre versioni di un'espressione: il modello corrente nel riquadro Test del sito Web [LUIS](luis-reference-regions.md) e le due versioni ai due diversi endpoint. 

Tutti i test con endpoint vengono conteggiati ai fini della quota di utilizzo. 

## <a name="do-not-log-tests"></a>Non registrare i test
Se si esegue il test rispetto a un endpoint e non si desidera registrare l'espressione, ricordarsi di usare la configurazione della stringa di query `logging=false`.

## <a name="where-to-find-utterances"></a>Dove trovare le espressioni
LUIS archivia tutte le espressioni registrate nel log di query, disponibile per il download nel portale LUIS dalla pagina di elenco delle **app** , oltre alle API di [creazione](https://go.microsoft.com/fwlink/?linkid=2092087)Luis. 

Tutte le espressioni di cui LUIS non è sicuro sono elencate nella pagina **[Review endpoint utterances](luis-how-to-review-endpoint-utterances.md)** (Esamina espressioni endpoint) del sito Web [LUIS](luis-reference-regions.md). 

## <a name="remember-to-train"></a>Ricordarsi di eseguire il training
Ricordarsi di [eseguire il training](luis-how-to-train.md) di LUIS dopo aver apportato modifiche al modello. Le modifiche all'app LUIS non compaiono nel test finché non si esegue il training dell'app. 

## <a name="best-practices"></a>Procedure consigliate
Informazioni sulle [procedure consigliate](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sul [test](luis-interactive-test.md) delle espressioni.
