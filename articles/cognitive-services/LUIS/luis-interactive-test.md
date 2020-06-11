---
title: Testare l'app nel portale di LUIS
description: Usare il servizio LUIS (Language Understanding) per lavorare continuamente sull'applicazione allo scopo di perfezionarla e migliorarne l'apprendimento del linguaggio.
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 574bacdb5e1f167c9c9174d4a119552391059004
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677738"
---
# <a name="test-your-luis-app-in-the-luis-portal"></a>Testare l'app LUIS nel portale LUIS

[Il test](luis-concept-test.md) di un'app costituisce un processo iterativo. Dopo aver eseguito il training dell'app LUIS, testarla con espressioni di esempio per verificare se le finalità e le entità vengono riconosciute correttamente. Se così non fosse, aggiornare l'app LUIS ed eseguire nuovamente il training e il test.

<!-- anchors for H2 name changes -->
<a name="train-your-app"></a>
<a name="test-your-app"></a>
<a name="access-the-test-page"></a>
<a name="luis-interactive-testing"></a>

## <a name="train-before-testing"></a>Esegui il training prima del test

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Per eseguire il test con la versione più recente dell'app attiva, selezionare **Train** dal menu in alto, prima del test.

## <a name="test-an-utterance"></a>Eseguire il test di un'espressione

L'espressione di test non deve essere esattamente identica a qualsiasi espressione di esempio nell'app. L'espressione di test deve includere la scelta di parole, la lunghezza delle frasi e l'utilizzo dell'entità previsti per un utente.

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .

1. Per accedere al pannello della diapositiva di **test** , selezionare **test** nel pannello superiore dell'applicazione.

    > [!div class="mx-imgBorder"]
    > ![Pagina di training & testare l'app](./media/luis-how-to-interactive-test/test.png)

1. Immettere un'espressione nella casella di testo e premere Invio. È possibile digitare il numero desiderato di espressioni in **Test**, ma solo un'espressione alla volta.

1. L'espressione, la finalità principale relativa e il punteggio vengono aggiunti all'elenco di espressioni nella casella di testo.

    > [!div class="mx-imgBorder"]
    > ![Il test interattivo identifica la finalità errata](./media/luis-how-to-interactive-test/test-weather-1.png)

## <a name="inspect-the-prediction"></a>Esaminare la stima

Esaminare i dettagli del risultato del test nel pannello di **controllo** .

1. Con il pannello a scorrimento **Test** aperto, selezionare **Inspect** (Ispeziona) per un'espressione da confrontare.

    > [!div class="mx-imgBorder"]
    > ![Selezionare il pulsante Ispeziona per visualizzare altri dettagli sui risultati del test](./media/luis-how-to-interactive-test/inspect.png)

1. Verrà visualizzato il pannello **ispezione** . Il pannello include la finalità con il punteggio più alto ed eventuali entità identificate. Il pannello mostra la stima dell'espressione selezionata.

    > [!div class="mx-imgBorder"]
    > ![Screenshot parziale del pannello test ispeziona](./media/luis-how-to-interactive-test/inspect-panel.png)

## <a name="add-to-example-utterances"></a>Aggiungi a espressioni di esempio

Dal Pannello ispezione è possibile aggiungere l'espressione di test a uno scopo selezionando **Aggiungi a espressioni di esempio**.

## <a name="disable-required-features"></a>Disabilitare le funzionalità richieste

Questo elemento consente di determinare se l'app sottoposta a training sta stimando correttamente le entità in base alle funzionalità richieste. L'impostazione predefinita prevede l'applicazione della funzionalità come richiesto durante la stima. Selezionare questa opzione per visualizzare l'elemento di stima se la funzionalità della sottoentità non è necessaria.

### <a name="when-to-disable-required-features"></a>Quando disabilitare le funzionalità necessarie

L'app sottoposta a training può prevedere una stima errata di un'entità appresa dal computer in base a uno dei seguenti:
* Etichetta di espressioni di esempio non corretta.
* La funzionalità richiesta non corrisponde al testo.

Un esempio è un'entità appresa dal computer con una sottoentità del nome di una persona.

:::image type="content" source="media/luis-how-to-interactive-test/disable-required-feature.png" alt-text="Screenshot dello schema di entità di LUIS Portal Machine-learned con la funzionalità richiesta":::

Un enunciato di esempio per questa entità appresa dal computer è: `Assign Bob Jones to work on the new security feature` .

L'estrazione dovrebbe essere `security feature` come descrizione del ticket e `Bob Jones` come ingegnere, due sottoentità dell' `Assign ticket` entità.

Per consentire la corretta stima della sottoentità, aggiungere la funzionalità predefinita Entity [PersonName](luis-reference-prebuilt-person.md) AA a `engineer` . Se si rende necessaria la funzionalità, significa che la sottoentità verrà estratta solo se per il testo è prevista l'entità precompilata PersonName. Ciò significa che qualsiasi nome nel testo che non esegue la stima con PersonName SubEntità non verrà restituito come sottoentità con etichetta, `engineer` .

Quando si usa il riquadro di test interattivo e si visualizza un'entità subenti, con una funzionalità obbligatoria, non esegue la stima, attiva/Rimuovi questa impostazione per verificare se la sottoentità verrebbe stimata senza che la funzionalità fosse richiesta. È possibile che la sottoentità possa essere stimata correttamente senza la funzionalità necessaria a causa dell'etichettatura corretta di espressioni di esempio.

## <a name="view-sentiment-results"></a>Visualizzare i risultati del sentiment

Se l'**analisi del sentiment** è stata configurata nella pagina **[Publish](luis-how-to-publish-app.md#enable-sentiment-analysis)** (Pubblica), i risultati del test includono il sentiment trovato nell'espressione.

## <a name="correct-matched-patterns-intent"></a>Correggere la finalità del criterio corrispondente

Se si usano [criteri](luis-concept-patterns.md) e l'espressione corrisponde a un criterio, ma è stata stimata la finalità errata, selezionare il collegamento **Edit** (Modifica) del criterio, quindi selezionare la finalità corretta.

## <a name="compare-with-published-version"></a>Eseguire il confronto con la versione pubblicata

È possibile testare la versione attiva dell'app con la versione [endpoint](luis-glossary.md#endpoint) pubblicata. Nel pannello **Inspect** (Ispeziona) selezionare **Compare with published** (Confronta con pubblicata). Eventuali test sul modello pubblicato vengono dedotti dal saldo della quota di sottoscrizione di Azure.

> [!div class="mx-imgBorder"]
> ![Compare with published (Confronta con pubblicata)](./media/luis-how-to-interactive-test/inspect-panel-compare.png)

## <a name="view-endpoint-json-in-test-panel"></a>Visualizzare l'endpoint JSON nel pannello test
È possibile visualizzare l'endpoint JSON restituito per il confronto selezionando **Show JSON view** (Mostra visualizzazione JSON).

> [!div class="mx-imgBorder"]
> ![Risposta JSON pubblicata](./media/luis-how-to-interactive-test/inspect-panel-compare-json.png)

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
