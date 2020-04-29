---
title: Testare l'app nel portale di LUIS
description: Usare il servizio LUIS (Language Understanding) per lavorare continuamente sull'applicazione allo scopo di perfezionarla e migliorarne l'apprendimento del linguaggio.
ms.topic: conceptual
ms.date: 11/19/2019
ms.openlocfilehash: ba7e23a72cd308dd4393bf9a581571e2bc9f5fa0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79219819"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testare l'app LUIS nel portale LUIS

[Il test](luis-concept-test.md) di un'app costituisce un processo iterativo. Dopo aver eseguito il training dell'app LUIS, testarla con espressioni di esempio per verificare se le finalità e le entità vengono riconosciute correttamente. Se così non fosse, aggiornare l'app LUIS ed eseguire nuovamente il training e il test.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Esegui il training prima del test

Per eseguire il test con la versione più recente dell'app attiva, selezionare **Train** dal menu in alto, prima del test.

## <a name="test-an-utterance"></a>Eseguire il test di un'espressione

L'espressione di test non deve essere esattamente identica a qualsiasi espressione di esempio nell'app. L'espressione di test deve includere la scelta di parole, la lunghezza delle frasi e l'utilizzo dell'entità previsti per un utente.

1. Accedere all'app selezionando il relativo nome nella pagina **My Apps** (App personali).

1. Per accedere al pannello della diapositiva di **test** , selezionare **test** nel pannello superiore dell'applicazione.

    > [!div class="mx-imgBorder"]
    > ![Pagina di training & testare l'app](./media/luis-how-to-interactive-test/test.png)

1. Immettere un'espressione nella casella di testo e premere Invio. È possibile digitare il numero desiderato di espressioni in **Test**, ma solo un'espressione alla volta.

1. L'espressione, la finalità principale relativa e il punteggio vengono aggiunti all'elenco di espressioni nella casella di testo.

    ![Il test interattivo identifica la finalità errata](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-score"></a>Ispezionare il punteggio

Esaminare i dettagli del risultato del test nel pannello di **controllo** .

1. Con il pannello a scorrimento **Test** aperto, selezionare **Inspect** (Ispeziona) per un'espressione da confrontare.

    ![Selezionare il pulsante Ispeziona per visualizzare altri dettagli sui risultati del test](./media/luis-how-to-interactive-test/inspect.png)

1. Verrà visualizzato il pannello **ispezione** . Il pannello include la finalità con il punteggio più alto ed eventuali entità identificate. Il pannello mostra il risultato dell'espressione selezionata.

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

## <a name="additional-settings-in-test-panel"></a>Impostazioni aggiuntive nel pannello test

### <a name="luis-endpoint"></a>Endpoint LUIS

Se si dispone di diversi endpoint LUIS, usare il collegamento **Additional Settings** (Impostazioni aggiuntive) nel riquadro Test's Published (Pubblicato test) per modificare l'endpoint usato per il test. Se non si sa quale endpoint usare, selezionare quello predefinito **Starter_Key** (Chiave di avvio).

> [!div class="mx-imgBorder"]
> ![Pannello test con il collegamento Additional Settings (Impostazioni avanzate) evidenziato](media/luis-how-to-interactive-test/additional-settings-v3-settings.png)


## <a name="batch-testing"></a>Test in batch
Vedere i [concetti](luis-concept-batch-test.md) del test in batch e imparare a [testare](luis-how-to-batch-test.md) un batch di espressioni.

## <a name="next-steps"></a>Passaggi successivi

Se il test indica che l'app LUIS non riconosce le finalità e le entità corrette, è possibile migliorare l'accuratezza dell'app LUIS etichettando più espressioni o aggiungendo funzionalità.

* [Etichettare le espressioni suggerite con LUIS](luis-how-to-review-endpoint-utterances.md)
* [Usare le funzionalità per migliorare le prestazioni dell'app LUIS](luis-how-to-add-features.md)
