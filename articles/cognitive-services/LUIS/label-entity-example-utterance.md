---
title: Etichettare un'entità in un'espressione di esempio
description: Informazioni su come etichettare un'entità di Machine Learning con sottoentità in un enunciato di esempio in una pagina di dettagli Intent del portale LUIS.
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: f576f42f46930d81212ff754a14ee30c75b79624
ms.sourcegitcommit: 32592ba24c93aa9249f9bd1193ff157235f66d7e
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85610490"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>Etichettare un'entità di Machine Learning in un enunciato di esempio

L'etichettatura di un'entità in un'espressione di esempio fornisce a LUIS un esempio di ciò che l'entità rappresenta e il punto in cui l'entità può essere visualizzata nell'espressione.

È possibile etichettare entità e sottoentità apprese dal computer.

Poiché non è possibile assegnare etichette a espressioni regolari, elenchi o entità predefinite, creare un'entità o una sottoentità, quindi aggiungere queste entità come funzionalità, quando applicabile, all'entità o all'entità secondaria.

## <a name="label-example-utterances-from-the-intent-detail-page"></a>Etichetta espressioni di esempio dalla pagina Dettagli Intent

Per etichettare esempi di entità all'interno dell'espressione, selezionare lo scopo dell'espressione.

1. Accedere al [portale LUIS](https://www.luis.ai) e quindi selezionare la **sottoscrizione** e la **risorsa di creazione** per vedere le app assegnate a tale risorsa.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Selezionare lo scopo che include le espressioni di esempio che si vuole etichettare per l'estrazione con un'entità.
1. Selezionare il testo che si vuole etichettare, quindi selezionare l'entità.

## <a name="two-techniques-to-label-entities"></a>Due tecniche per etichettare le entità

Nella pagina Dettagli finalità sono supportate due tecniche di assegnazione di etichette.
* Selezionare entità o sottoentità da [tavolozza entità](#label-with-the-entity-palette-visible) , quindi selezionare nel testo enunciato di esempio. Questa è la tecnica consigliata, in quanto è possibile verificare visivamente che si stia utilizzando l'entità o sottoentità corretta, in base allo schema.
* Selezionare prima nel testo dell'espressione di esempio. Quando si esegue questa operazione, viene visualizzato un menu a comparsa con le [Opzioni di assegnazione di etichette](#how-to-label-entity-from-in-place-menu) .

## <a name="label-with-the-entity-palette-visible"></a>Etichetta con la tavolozza delle entità visibile

Dopo aver [pianificato lo schema con le entità](luis-how-plan-your-app.md), è possibile lasciare visibile la **tavolozza delle entità** durante l'assegnazione di etichette. Il **tavolozza delle entità** è un promemoria delle entità pianificate per l'estrazione.

Per accedere alla **tavolozza delle entità**, selezionare il **@** simbolo nella barra degli strumenti contestuale sopra l'elenco di espressioni di esempio.

> [!div class="mx-imgBorder"]
> ![Screenshot della tavolozza delle entità nella pagina Dettagli finalità.](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="how-to-label-entity-from-entity-palette"></a>Come etichettare un'entità da tavolozza entità

La tavolozza delle entità offre un'alternativa alla precedente esperienza di etichettatura. Consente di selezionare il testo con il pennello per etichettarlo immediatamente con un'entità.

1. Aprire la tavolozza delle entità selezionando il **@** simbolo nella parte superiore destra della tabella dell'espressione.

2. Selezionare l'entità dalla tavolozza che si desidera assegnare all'etichetta. Questa azione è indicata visivamente con un nuovo cursore. Il cursore segue il mouse durante lo spostamento nel portale LUIS.

3. Nell'espressione di esempio, _dipingere_ l'entità con il cursore.

    > [!div class="mx-imgBorder"]
    > ![Tavolozza delle entità per l'entità Machine Learning](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="adding-entity-as-a-feature-from-the-entity-palette"></a>Aggiunta di un'entità come funzionalità dalla tavolozza delle entità

La sezione inferiore della tavolozza delle entità consente di aggiungere funzionalità all'entità attualmente selezionata. È possibile scegliere tra tutte le entità e gli elenchi di frasi esistenti oppure creare un nuovo elenco di frasi.

> [!div class="mx-imgBorder"]
> ![Screenshot della tavolozza entità con entità come funzionalità](media/label-utterances/entity-palette-entity-as-a-feature.png)

## <a name="labeling-entity-roles"></a>Etichettatura dei ruoli di entità

I ruoli entità sono contrassegnati con la **tavolozza delle entità**.

1. Nella pagina di dettagli della finalità selezionare il **pannello di entità** sulla barra degli strumenti contestuali.
1. Una volta aperto il pannello di entità, selezionare l'entità nell'elenco.
1. Sotto l'elenco di entità selezionare un ruolo esistente.
1. Nel testo dell'espressione di esempio, etichettare il testo con il ruolo dell'entità.

## <a name="how-to-label-entity-from-in-place-menu"></a>Come etichettare un'entità dal menu sul posto

L'assegnazione di etichette sul posto consente di selezionare rapidamente il testo all'interno dell'enunciato ed etichettarlo. È anche possibile creare un'entità di Machine Learning o un elenco di entità dal testo con etichetta.

Si consideri l'espressione di esempio, `hi, please I want a cheese pizza in 20 minutes` .

Selezionare il testo più a sinistra, quindi selezionare il testo più a destra dell'entità, quindi scegliere l'entità con cui si vuole assegnare un'etichetta dal menu sul posto.

> [!div class="mx-imgBorder"]
> ![Etichetta entità di Machine Learning completa](media/label-utterances/label-steps-in-place-menu.png)

## <a name="review-labeled-text"></a>Esaminare il testo etichettato

Dopo l'etichettatura controllare l'espressione di esempio per verificare che la sezione di testo selezionata sia stata sottolineata con l'entità scelta. La linea continua indica che il testo è stato etichettato.

> [!div class="mx-imgBorder"]
> ![Entità di Machine Learning completa con etichetta](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Confermare l'entità stimata

Se è presente una casella tratteggiata a linee intorno all'intervallo di testo, significa che il testo viene stimato ma _non ancora contrassegnato_. Per attivare la stima in un'etichetta, selezionare la riga espressione, quindi selezionare **conferma entità** dalla barra degli strumenti contestuale.

## <a name="relabeling-over-existing-entities"></a>Modifica dell'etichetta sulle entità esistenti

Se si modifica l'etichetta del testo già etichettato, LUIS può suddividere o unire le etichette esistenti.

## <a name="labeling-for-punctuation"></a>Assegnazione di etichette per la punteggiatura

Non è necessario etichettare per la punteggiatura. Usare [le impostazioni dell'applicazione](luis-reference-application-settings.md) per controllare il modo in cui la punteggiatura influisca sulle stime dell'espressione.

## <a name="unlabel-entities"></a>Rimuovere l'etichettatura delle entità

> [!NOTE]
> È possibile senza etichettare solo le entità apprese dal computer. Non è possibile etichettare o anetichettare entità di espressioni regolari, entità di elenco o entità predefinite.

Per denominare un'entità, selezionare l'entità e scegliere **unlabel** dal menu sul posto.

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra l'entità senza etichetta](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>Assegnazione automatica di etichette per entità padre e figlio

Se si etichetta un'entità padre, qualsiasi sottoentità che può essere stimata in base alla versione attualmente sottoposta a training verrà etichettata.

Se si esegue l'assegnazione di etichette per una sottoentità, l'elemento padre verrà etichettato automaticamente.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>Assegnazione automatica di etichette per entità non acquisite dal computer

Le entità non acquisite dal computer includono entità predefinite, entità di espressioni regolari, entità dell'elenco e pattern. Any. Queste entità vengono etichettate automaticamente da LUIS in modo che non debbano essere etichettate manualmente dagli utenti.

## <a name="intent-prediction-errors"></a>Errori di stima per finalità

Un errore di stima preventivo indica che l'espressione di esempio, data l'app attualmente sottoposta a training, non verrà prevista per lo scopo.

Informazioni su come [visualizzare questi errori](luis-how-to-add-intents.md#intent-prediction-errors) nella pagina dei dettagli sullo scopo.

## <a name="entity-prediction-errors"></a>Errori di stima delle entità

Gli errori di stima delle entità indicano che l'entità stimata non corrisponde all'entità etichettata. Questo tipo di errore viene visualizzato con un indicatore di attenzione accanto all'espressione.

> [!div class="mx-imgBorder"]
> ![Tavolozza delle entità per l'entità Machine Learning](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Passaggi successivi

Usare il [dashboard](luis-how-to-use-dashboard.md) e [rivedere le espressioni di endpoint](luis-how-to-review-endpoint-utterances.md) per migliorare la qualità della stima di un'app.
