---
title: Etichettare un'entità in un'espressione di esempio
titleSuffix: Azure Cognitive Services
description: Informazioni su come etichettare un'entità basata su apprendimento automatico con sottocomponenti in un'espressione di esempio in una pagina dei dettagli delle finalità del portale LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: diberry
ms.openlocfilehash: 58e813d30273db4e011039aa43cd59c61507895e
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2019
ms.locfileid: "74383711"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Etichettare un'entità basata su apprendimento automatico in un'espressione di esempio

L'etichettatura di un'entità in un'espressione di esempio fornisce a LUIS un esempio di ciò che l'entità rappresenta e il punto in cui l'entità può essere visualizzata nell'espressione. 

## <a name="labeling-machine-learned-entity"></a>Assegnazione di un'etichetta a un'entità basata su apprendimento automatico

Considerare la frase `hi, please I want a cheese pizza in 20 minutes`. 

1. Selezionare il testo più a sinistra, quindi selezionare il testo più a destra dell'entità e infine scegliere l'entità con cui si vuole etichettare, in questo caso l'ordine completo. L'_ordine completo_ è etichettato nell'immagine seguente.

    > [!div class="mx-imgBorder"]
    > ![Etichettare un'entità completa basata su apprendimento automatico](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Selezionare l'entità dalla finestra popup. L'entità etichettata dell'ordine completo della pizza include tutte le parole (da sinistra a destra in inglese) etichettate. 

## <a name="review-labeled-text"></a>Esaminare il testo etichettato

Dopo l'etichettatura controllare l'espressione di esempio per verificare che la sezione di testo selezionata sia stata sottolineata con l'entità scelta. La linea continua indica che il testo è stato etichettato. 

> [!div class="mx-imgBorder"]
> ![Entità completa basata su apprendimento automatico con etichetta](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="confirm-predicted-entity"></a>Confermare l'entità stimata

Se è presente un riquadro con linee tratteggiate intorno alla sezione di testo e il nome dell'entità si trova sopra l'espressione, significa che il testo è stato stimato ma non _ancora etichettato_. Per attivare la stima in un'etichetta, selezionare la riga dell'espressione e quindi selezionare **Confirm entity predictions** (Conferma stime entità).

> [!div class="mx-imgBorder"]
> ![Stimare un'entità completa basata su apprendimento automatico](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

In alternativa, è possibile selezionare il nome dell'entità sopra il testo e quindi selezionare **Confirm Prediction** (Conferma stima) nel menu visualizzato.

> [!div class="mx-imgBorder"]
> ![Stimare un'entità completa basata su apprendimento automatico con menu](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted-menu.png)

## <a name="label-entity-by-painting-with-entity-palette-cursor"></a>Etichettare un'entità mediante disegno con il cursore della tavolozza delle entità

La tavolozza delle entità offre un'alternativa alla precedente esperienza di etichettatura. Consente di selezionare il testo con il pennello per etichettarlo immediatamente con un'entità.

1. Aprire la tavolozza delle entità selezionando l'icona dell'evidenziatore in alto a destra della tabella delle espressioni. 

    > [!div class="mx-imgBorder"]
    > ![Tavolozza delle entità per un'entità con apprendimento automatico](media/label-utterances/example-1-label-machine-learned-entity-palette.png)

2. Selezionare il componente entità. Questa azione è indicata visivamente con un nuovo cursore. Il cursore segue il mouse durante la navigazione nel portale. 

    > [!div class="mx-imgBorder"]
    > ![Tavolozza delle entità per un'entità con apprendimento automatico](media/label-utterances/example-1-label-machine-learned-entity-palette-menu.png)

3. Nell'espressione di esempio, _dipingere_ l'entità con il cursore.

    > [!div class="mx-imgBorder"]
    > ![Tavolozza delle entità per un'entità con apprendimento automatico](media/label-utterances/example-1-label-machine-learned-entity-palette-label-action.png)

## <a name="labeling-subcomponents-of-a-machine-learned-entity"></a>Etichettatura di sottocomponenti di un'entità con apprendimento automatico

I sottocomponenti delle entità vengono etichettati esattamente allo stesso modo delle entità di livello superiore. Quando si seleziona il testo, le entità disponibili nella finestra popup sono relative al contesto in cui viene visualizzato il testo. Ad esempio, se si dispone di un'entità basata su apprendimento automatico a 5 livelli e si seleziona un testo etichettato con il primo e il secondo livello (indicato da un nome di entità con etichetta sotto l'espressione di esempio), le entità disponibili nella finestra popup sono limitate al contesto dei componenti del terzo livello. Per etichettare il testo con altre entità, selezionare l'opzione **Label as another entity** (Etichetta come un'altra entità).

> [!div class="mx-imgBorder"]
> ![Tavolozza delle entità per un'entità con apprendimento automatico](media/label-utterances/example-1-label-machine-learned-entity-subcomponent.png)

I sottocomponenti possono essere etichettati solo se anche l'elemento padre è etichettato. 

## <a name="unlabel-entities"></a>Rimuovere l'etichettatura delle entità

Per rimuovere l'etichettatura di un'entità, selezionare il nome dell'entità sotto il testo e selezionare **Unlabel** (Rimuovi etichettatura). Se l'entità di cui si sta provando a rimuovere l'etichettatura contiene sottocomponenti etichettati, è necessario rimuovere prima l'etichettatura dei sottocomponenti. 

## <a name="editing-labels-using-the-entity-palette"></a>Modifica delle etichette con la tavolozza delle entità

Se si commette un errore durante l'etichettatura, la tavolozza delle entità è uno strumento semplice che consente modifiche rapide. Se, ad esempio, un'etichetta di entità si estende per errore su una parola in più e sono già stati etichettati i sottocomponenti, è possibile usare la tavolozza delle entità per selezionare con il pennello la sezione di parole più breve desiderata.

Ad esempio:

1. Il sottocomponente Pizza Type si estende su "cheese pizza with", che include la parola "with" in più non corretta.

    > [!div class="mx-imgBorder"]
    > ![Tavolozza delle entità per un'entità con apprendimento automatico](media/label-utterances/edit-label-with-palette-1.png)

2. Usare la tavolozza delle entità per scegliere Pizza Type e selezionare con il pennello "cheese pizza". Il risultato è che ora solo il testo cheese pizza è etichettato come Pizza Type.

    > [!div class="mx-imgBorder"]
    > ![Tavolozza delle entità per un'entità con apprendimento automatico](media/label-utterances/edit-label-with-palette-2.png)


## <a name="labels-for-matching-text-entities"></a>Etichette per entità con testo corrispondente

Le entità con testo corrispondente includono entità predefinite, entità di espressioni regolari, entità di elenco ed entità pattern.any. Queste entità vengono etichettate automaticamente da LUIS in modo che non debbano essere etichettate manualmente dagli utenti.

## <a name="entity-prediction-errors"></a>Errori di stima delle entità

Gli errori di stima delle entità indicano che l'entità stimata non corrisponde all'entità etichettata. Questo tipo di errore viene visualizzato con un indicatore di attenzione accanto all'espressione.

> [!div class="mx-imgBorder"]
> ![Tavolozza delle entità per un'entità con apprendimento automatico](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Passaggi successivi

Usare il [dashboard](luis-how-to-use-dashboard.md) e [rivedere le espressioni di endpoint](luis-how-to-review-endpoint-utterances.md) per migliorare la qualità della stima di un'app.