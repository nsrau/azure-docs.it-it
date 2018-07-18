---
title: Eseguire il test dell'app LUIS - Azure | Microsoft Docs
description: Usare il servizio LUIS (Language Understanding) per lavorare continuamente sull'applicazione allo scopo di perfezionarla e migliorarne l'apprendimento del linguaggio.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 8c702d2adbadd2736eed05c7580e8aabf69affbf
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266329"
---
# <a name="testing-in-luis"></a>Test in LUIS

Il processo di test consiste nel fornire espressioni di esempio a LUIS e nell'ottenere una risposta di finalità ed entità riconosciute da LUIS. 

È possibile [testare](interactive-test.md) LUIS in modo interattivo, un'espressione alla volta, o fornire un [batch](luis-concept-batch-test.md) di espressioni. Attraverso il test, si confronta il modello [attivo](luis-concept-version.md#active-version) corrente al modello pubblicato. 

<a name="A-test-score"></a>
<a name="Score-all-intents"></a>
<a name="E-(exponent)-notation"></a>
## <a name="what-is-a-score-in-testing"></a>Cos'è il punteggio nell'ambito del test?
Vedere i concetti relativi al [punteggio di stima](luis-concept-prediction-score.md) per ulteriori informazioni sui punteggi di stima.

## <a name="interactive-testing"></a>Test interattivi
I test interattivi vengono eseguiti nel pannello **Test** del sito Web. È possibile immettere un'espressione per vedere in che modo vengono identificate le finalità e le entità e quale punteggio viene attribuito loro. Se LUIS non stima le finalità e le entità come previsto in un'espressione nel riquadro di test, copiarla nella pagina **Intent** (Finalità) come nuova espressione. Etichettare le parti dell'espressione, quindi eseguire il training di LUIS. 

## <a name="batch-testing"></a>Test in batch
Vedere [Test in batch](luis-concept-batch-test.md) se si testa più di un'espressione alla volta.

## <a name="endpoint-testing"></a>Test con endpoint
È possibile eseguire il test con l'[endpoint](luis-glossary.md#endpoint) con un massimo di due versioni dell'app. Con la versione principale o in tempo reale dell'app impostata come endpoint di **produzione**, aggiungere una seconda versione all'endpoint di **staging**. Questo approccio fornisce tre versioni di un'espressione: il modello corrente nel riquadro Test del sito Web [LUIS][LUIS] e le due versioni ai due diversi endpoint. 

Tutti i test con endpoint vengono conteggiati ai fini della quota di utilizzo. 

## <a name="do-not-log-tests"></a>Non registrare i test
Se si esegue il test rispetto a un endpoint e non si desidera registrare l'espressione, ricordarsi di usare la configurazione della stringa di query `logging=false`.

## <a name="where-to-find-utterances"></a>Dove trovare le espressioni
LUIS archivia tutte le espressioni registrate nel log di query, disponibile per il download nella pagina [Apps][LUIS] (App) del sito Web **LUIS** e nelle [API di creazione](https://aka.ms/luis-authoring-apis) di LUIS. 

Tutte le espressioni di cui LUIS non è sicuro sono elencate nella pagina **[Review endpoint utterances](label-suggested-utterances.md)** (Esamina espressioni endpoint) del sito Web [LUIS][LUIS]. 

![Esaminare le espressioni endpoint](./media/luis-concept-test/review-endpoint-utterances.png)
 
## <a name="remember-to-train"></a>Ricordarsi di eseguire il training
Ricordarsi di [eseguire il training](luis-how-to-train.md) di LUIS dopo aver apportato modifiche al modello. Le modifiche all'app LUIS non compaiono nel test finché non si esegue il training dell'app. 

## <a name="best-practices"></a>Procedure consigliate
Apprendere le [procedure consigliate](luis-concept-best-practices.md).

## <a name="next-steps"></a>Passaggi successivi

* Ulteriori informazioni sul [test](interactive-test.md) delle espressioni.

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions