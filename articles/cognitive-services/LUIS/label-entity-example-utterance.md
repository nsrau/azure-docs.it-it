---
title: Etichettare un'entità in un'espressione di esempio
description: Informazioni su come etichettare un'entità di Machine Learning con sottocomponenti in un enunciato di esempio in una pagina di dettagli Intent del portale LUIS.
ms.topic: conceptual
ms.date: 05/17/2020
ms.openlocfilehash: 0181057bd693280223806e9b5b7cd8c7f7345f79
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83683772"
---
# <a name="label-machine-learning-entity-in-an-example-utterance"></a>Etichettare un'entità di Machine Learning in un enunciato di esempio

L'etichettatura di un'entità in un'espressione di esempio fornisce a LUIS un esempio di ciò che l'entità rappresenta e il punto in cui l'entità può essere visualizzata nell'espressione.

## <a name="label-example-utterances-from-the-intent-detail-page"></a>Etichetta espressioni di esempio dalla pagina Dettagli Intent

1. Accedere al [portale Luis](https://www.luis.ai)e selezionare la **sottoscrizione** e la risorsa di **creazione** per visualizzare le app assegnate a tale risorsa di creazione.
1. Per aprire l'app, selezionarne il nome nella pagina **app personali** .
1. Selezionare lo scopo che include le espressioni di esempio che si vuole etichettare per l'estrazione con un'entità.
1. Selezionare il testo che si vuole etichettare, quindi selezionare l'entità.

## <a name="label-with-the-entity-palette-visible"></a>Etichetta con la tavolozza delle entità visibile

Dopo aver pianificato lo schema con le entità, è possibile lasciare visibile la **tavolozza delle entità** durante l'assegnazione di etichette. Il **tavolozza delle entità** è un promemoria delle entità pianificate per l'estrazione.

Per accedere alla **tavolozza delle entità**, selezionare il **@** simbolo nella barra degli strumenti contestuale sopra l'elenco di espressioni di esempio.

> [!div class="mx-imgBorder"]
> ![Screenshot della tavolozza delle entità nella pagina Dettagli finalità.](media/label-utterances/entity-palette-from-tool-bar.png)

## <a name="label-entity-from-in-place-menu"></a>Etichetta entità dal menu sul posto

Si consideri l'espressione di esempio, `hi, please I want a cheese pizza in 20 minutes` .

1. Selezionare il testo più a sinistra, quindi selezionare il testo più a destra dell'entità, quindi scegliere l'entità con cui si vuole assegnare un'etichetta dal menu sul posto.

    > [!div class="mx-imgBorder"]
    > ![Etichetta entità di Machine Learning completa](media/label-utterances/label-steps-in-place-menu.png)


## <a name="label-entity-from-entity-palette"></a>Etichetta entità da tavolozza entità

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

## <a name="review-labeled-text"></a>Esaminare il testo etichettato

Dopo l'etichettatura controllare l'espressione di esempio per verificare che la sezione di testo selezionata sia stata sottolineata con l'entità scelta. La linea continua indica che il testo è stato etichettato.

> [!div class="mx-imgBorder"]
> ![Entità di Machine Learning completa con etichetta](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Confermare l'entità stimata

Se è presente una casella tratteggiata a linee intorno all'intervallo di testo, significa che il testo viene stimato ma _non ancora contrassegnato_. Per attivare la stima in un'etichetta, selezionare la riga espressione, quindi selezionare **conferma entità** dalla barra degli strumenti contestuale.

## <a name="relabeling-over-existing-entities"></a>Modifica dell'etichetta sulle entità esistenti

Se si modifica l'etichetta del testo già etichettato, LUIS può suddividere o unire le etichette esistenti.

## <a name="labeling-for-punctuation"></a>Assegnazione di etichette per la punteggiatura

Non è necessario etichettare per la punteggiatura. Usare i _moduli Word_ [delle impostazioni dell'applicazione](luis-reference-application-settings.md) per controllare se la punteggiatura influisca sulle stime dell'espressione.

## <a name="unlabel-entities"></a>Rimuovere l'etichettatura delle entità

> [!NOTE]
> È possibile senza etichettare solo le entità apprese dal computer.

Per denominare un'entità, selezionare l'entità e scegliere **unlabel** dal menu sul posto.

> [!div class="mx-imgBorder"]
> ![Screenshot che mostra l'entità senza etichetta](media/label-utterances/unlabel-entity-using-in-place-menu.png)

## <a name="automatic-labeling-for-parent-and-child-entities"></a>Assegnazione automatica di etichette per entità padre e figlio

Se si etichetta un'entità padre, qualsiasi sottoentità che può essere stimata in base alla versione attualmente sottoposta a training verrà etichettata.

Se si esegue l'assegnazione di etichette per una sottoentità, l'elemento padre verrà etichettato automaticamente.

## <a name="automatic-labeling-for-non-machine-learned-entities"></a>Assegnazione automatica di etichette per entità non acquisite dal computer

Le entità non acquisite dal computer includono entità predefinite, entità di espressioni regolari, entità dell'elenco e pattern. Any. Queste entità vengono etichettate automaticamente da LUIS in modo che non debbano essere etichettate manualmente dagli utenti.

## <a name="entity-prediction-errors"></a>Errori di stima delle entità

Gli errori di stima delle entità indicano che l'entità stimata non corrisponde all'entità etichettata. Questo tipo di errore viene visualizzato con un indicatore di attenzione accanto all'espressione.

> [!div class="mx-imgBorder"]
> ![Tavolozza delle entità per l'entità Machine Learning](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Passaggi successivi

Usare il [dashboard](luis-how-to-use-dashboard.md) e [rivedere le espressioni di endpoint](luis-how-to-review-endpoint-utterances.md) per migliorare la qualità della stima di un'app.