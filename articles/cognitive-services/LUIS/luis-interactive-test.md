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
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 628547e8254bb0055cf1f09af50e79b68311a759
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221765"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testare l'app LUIS nel portale LUIS

[Testing](luis-concept-test.md) an app is an iterative process. Dopo aver eseguito il training dell'app LUIS, testarla con espressioni di esempio per verificare se le finalità e le entità vengono riconosciute correttamente. Se così non fosse, aggiornare l'app LUIS ed eseguire nuovamente il training e il test. 

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Train before testing

In order to test against the most recent version of the active app, select **Train** from the top menu, before testing. 

## <a name="test-an-utterance"></a>Eseguire il test di un'espressione

The test utterance should not be exactly the same as any example utterances in the app. The test utterance should include word choice, phrase length, and entity usage you expect for a user. 

1. Accedere all'app selezionando il relativo nome nella pagina **App personali**. 

1. Per accedere al pannello a scorrimento **Test**, selezionare **Test** nel pannello superiore dell'applicazione.

    > [!div class="mx-imgBorder"]
    > ![Train & Test App page](./media/luis-how-to-interactive-test/test.png)

1. Immettere un'espressione nella casella di testo e premere Invio. È possibile digitare il numero desiderato di espressioni in **Test**, ma solo un'espressione alla volta.

1. L'espressione, la finalità principale relativa e il punteggio vengono aggiunti all'elenco di espressioni nella casella di testo.

    ![Il test interattivo identifica la finalità errata](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Punteggio dell'ispezione

È possibile ispezionare i dettagli dei risultati del test nel pannello **Inspect** (Ispeziona). 
 
1. Con il pannello a scorrimento **Test** aperto, selezionare **Inspect** (Ispeziona) per un'espressione da confrontare. 

    ![Selezionare il pulsante Ispeziona per visualizzare altri dettagli sui risultati del test](./media/luis-how-to-interactive-test/inspect.png)

1. Viene visualizzato il pannello **Inspection** (Ispezione). Il pannello include la finalità con il punteggio più alto ed eventuali entità identificate. Nel pannello viene mostrato il risultato della frase selezionata.

    ![Il pannello include la finalità con il punteggio più alto ed eventuali entità identificate. Nel pannello viene mostrato il risultato della frase selezionata.](./media/luis-how-to-interactive-test/inspect-panel.png)

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

> [!div class="mx-imgBorder"]
> ![Test panel with Additional Settings link highlighted](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)

<!--
###  View Bing Spell Check corrections in test panel

Requirements to view the spelling corrections: 

* Published app
* Bing Spell Check [service key](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api). The service key is not stored and needs to be reset for each browser session. 

Use the following procedure to include the [Bing Spell Check v7](https://azure.microsoft.com/services/cognitive-services/spell-check/) service  in the Test pane results. 

1. In the **Test** pane, enter an utterance. When the utterance is predicted, select **[Inspect](#inspect-score)** underneath the utterance you entered. 

1. When the **Inspect** panel opens, select **[Compare with Published](#compare-with-published-version)**. 

1. When the **Published** panel opens, select **[Additional Settings](#additional-settings-in-test-panel)**.

1. In the pop-up dialog, check **Enable Bing Spell Check** and enter the key, then select **Done**. 
    ![Enter Bing Spell Check service key](./media/luis-how-to-interactive-test/interactive-with-spell-check-service-key-text.png)

1. Enter a query with an incorrect spelling such as `book flite to seattle` and select enter. The incorrect spelling of the word `flite` is replaced in the query sent to LUIS and the resulting JSON shows both the original query, as `query`, and the corrected spelling in the query, as `alteredQuery`.

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
-->

## <a name="batch-testing"></a>Test in batch
Vedere i [concetti](luis-concept-batch-test.md) del test in batch e imparare a [testare](luis-how-to-batch-test.md) un batch di espressioni.

## <a name="next-steps"></a>Passaggi successivi

Se il test indica che l'app LUIS non riconosce le finalità e le entità corrette, è possibile migliorare l'accuratezza dell'app LUIS etichettando più espressioni o aggiungendo funzionalità. 

* [Etichettare le espressioni suggerite con LUIS](luis-how-to-review-endpoint-utterances.md) 
* [Usare le funzionalità per migliorare le prestazioni dell'app LUIS](luis-how-to-add-features.md) 
