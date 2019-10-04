---
title: Testare l'app nel portale di LUIS
titleSuffix: Azure Cognitive Services
description: Usare il servizio LUIS (Language Understanding) per lavorare continuamente sull'applicazione allo scopo di perfezionarla e migliorarne l'apprendimento del linguaggio.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 5736667acfa510d89e549afd4d0f5c61a833dd1c
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932743"
---
<a name="train-your-app"></a>

# <a name="test-your-luis-app-in-the-luis-portal"></a>Testare l'app LUIS nel portale LUIS

Il [test](luis-concept-test.md) di un'app è un processo iterativo. Dopo aver eseguito il training dell'app LUIS, testarla con espressioni di esempio per verificare se le finalità e le entità vengono riconosciute correttamente. Se così non fosse, aggiornare l'app LUIS ed eseguire nuovamente il training e il test. 

<!-- anchors for H2 name changes -->
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="test-an-utterance"></a>Eseguire il test di un'espressione

1. Accedere all'app selezionando il relativo nome nella pagina **My Apps** (App personali). 

1. Per accedere al pannello a scorrimento **Test**, selezionare **Test** nel pannello superiore dell'applicazione.

    ![Pagina Train & Test App (Esegui il training e il test dell'app)](./media/luis-how-to-interactive-test/test.png)

1. Immettere un'espressione nella casella di testo e premere Invio. È possibile digitare il numero desiderato di espressioni in **Test**, ma solo un'espressione alla volta.

1. L'espressione, la finalità principale relativa e il punteggio vengono aggiunti all'elenco di espressioni nella casella di testo.

    ![Il test interattivo identifica la finalità errata](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Ispezionare il punteggio

È possibile ispezionare i dettagli dei risultati del test nel pannello **Inspect** (Ispeziona). 
 
1. Con il pannello a scorrimento **Test** aperto, selezionare **Inspect** (Ispeziona) per un'espressione da confrontare. 

    ![Selezionare il pulsante Ispeziona per visualizzare altri dettagli sui risultati del test](./media/luis-how-to-interactive-test/inspect.png)

1. Viene visualizzato il pannello **Inspection** (Ispezione). Il pannello include la finalità con il punteggio più alto ed eventuali entità identificate. Il pannello mostra il risultato dell'espressione selezionata.

    ![Il pannello include la finalità con il punteggio più alto ed eventuali entità identificate. Il pannello mostra il risultato dell'espressione selezionata.](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="correct-top-scoring-intent"></a>Finalità con punteggio più alto corretta

1. Se la finalità con punteggio più alto non è corretta, selezionare il pulsante **Edit** (Modifica).

1.  Nell'elenco a discesa selezionare la finalità corretta per l'espressione.

    ![Selezionare la finalità corretta](./media/luis-how-to-interactive-test/intent-select.png)

## <a name="view-sentiment-results"></a>Visualizzare i risultati del sentiment

Se l'**analisi del sentiment** è stata configurata nella pagina **[Publish](luis-how-to-publish-app.md#enable-sentiment-analysis)** (Pubblica), i risultati del test includono il sentiment trovato nell'espressione. 

![Immagine del riquadro Test con analisi del sentiment](./media/luis-how-to-interactive-test/sentiment.png)

## <a name="correct-matched-patterns-intent"></a>Correggere la finalità del criterio corrispondente

Se si usano [criteri](luis-concept-patterns.md) e l'espressione corrisponde a un criterio, ma è stata stimata la finalità errata, selezionare il collegamento **Edit** (Modifica) del criterio, quindi selezionare la finalità corretta.

## <a name="compare-with-published-version"></a>Eseguire il confronto con la versione pubblicata

È possibile testare la versione attiva dell'app con la versione [endpoint](luis-glossary.md#endpoint) pubblicata. Nel pannello **Inspect** (Ispeziona) selezionare **Compare with published** (Confronta con pubblicata). Eventuali test sul modello pubblicato vengono dedotti dal saldo della quota di sottoscrizione di Azure. 

![Compare with published (Confronta con pubblicata)](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Visualizzare l'endpoint JSON nel pannello test
È possibile visualizzare l'endpoint JSON restituito per il confronto selezionando **Show JSON view** (Mostra visualizzazione JSON).

![Risposta JSON pubblicata](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

<!--Service name is 'Bing Spell Check v7 API' in the portal-->
## <a name="additional-settings-in-test-panel"></a>Impostazioni aggiuntive nel pannello test

### <a name="luis-endpoint"></a>Endpoint LUIS

Se si dispone di diversi endpoint LUIS, usare il collegamento **Additional Settings** (Impostazioni aggiuntive) nel riquadro Test's Published (Pubblicato test) per modificare l'endpoint usato per il test. Se non si sa quale endpoint usare, selezionare quello predefinito **Starter_Key** (Chiave di avvio). 

![Pannello test con il collegamento Additional Settings (Impostazioni avanzate) evidenziato](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key.png)


### <a name="view-bing-spell-check-corrections-in-test-panel"></a>Visualizzare le correzioni del Controllo ortografico Bing nel pannello test

Requisiti per visualizzare le correzioni ortografiche: 

* App pubblicata
* [Chiave del servizio](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api) Controllo ortografico Bing. La chiave del servizio non è archiviata e deve essere reimpostata per ogni sessione del browser. 

Usare la procedura seguente per includere il servizio [Controllo ortografico Bing v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) nei risultati del riquadro Test. 

1. Nel riquadro **Test** immettere un'espressione. Quando viene prevista l'espressione, selezionare **[Inspect](#inspect-score)** (Ispeziona) sotto l'espressione immessa. 

1. Quando si apre il pannello **Inspect** (Ispeziona), selezionare **[Compare with Published](#compare-with-published-version)** (Confronta con pubblicata). 

1. Quando si apre il pannello **Published** (Pubblicata) selezionare **[Additional Settings](#additional-settings-in-test-panel)** (Impostazioni aggiuntive).

1. Nella finestra di dialogo popup selezionare **abilita controllo ortografico Bing** e immettere la chiave e quindi fare clic su **fine**. 
    ![Immettere la chiave del servizio Controllo ortografico Bing](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Immettere una query con un'ortografia errata, ad esempio `book flite to seattle` e premere Invio. L'ortografia errata della parola `flite` viene sostituita nella query inviata a LUIS e il JSON risultante mostra sia la query originale, `query`, sia l'ortografia corretta nella query, `alteredQuery`.

<a name="json-file-with-no-duplicates"></a>
<a name="import-a-dataset-file-for-batch-testing"></a>
<a name="export-rename-delete-or-download-dataset"></a>
<a name="run-a-batch-test-on-your-trained-app"></a>
<a name="access-batch-test-result-details-in-a-visualized-view"></a>
<a name="filter-chart-results-by-intent-or-entity"></a>
<a name="investigate-false-sections"></a>
<a name="view single-point utterance data"></a>
<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="batch-testing"></a>Test in batch
Vedere i [concetti](luis-concept-batch-test.md) del test in batch e imparare a [testare](luis-how-to-batch-test.md) un batch di espressioni.

## <a name="next-steps"></a>Passaggi successivi

Se il test indica che l'app LUIS non riconosce le finalità e le entità corrette, è possibile migliorare l'accuratezza dell'app LUIS etichettando più espressioni o aggiungendo funzionalità. 

* [Etichettare espressioni suggerite con LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Usare le funzionalità per migliorare le prestazioni dell'app LUIS](luis-how-to-add-features.md) 
