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
ms.date: 11/15/2019
ms.author: diberry
ms.openlocfilehash: 87b7375b2da0effbc18fff6a7e9d67383c93a403
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/16/2019
ms.locfileid: "74134121"
---
# <a name="label-machine-learned-entity-in-an-example-utterance"></a>Etichettare un'entità basata su apprendimento automatico in un'espressione di esempio

L'assegnazione di etichette a un'entità in un'espressione di esempio mostra che LUIS dispone di un esempio dell'entità e il punto in cui l'entità può essere visualizzata nell'espressione. 

## <a name="labeling-machine-learned-entity"></a>Assegnazione di un'etichetta a un'entità basata su apprendimento automatico

Considerare la frase `hi, please I want a cheese pizza in 20 minutes`. 

1. Selezionare il testo più a sinistra, quindi il testo più a destra dell'entità. L'_ordine completo_ è etichettato nell'immagine seguente.

    > [!div class="mx-imgBorder"]
    > ![Etichettare un'entità completa basata su apprendimento automatico](media/label-utterances/example-1-label-machine-learned-entity-complete-order.png)

1. Selezionare l'entità dalla finestra popup. L'entità etichettata dell'ordine completo della pizza include tutte le parole (da sinistra a destra in inglese) etichettate. 

> [!TIP]
> Le entità disponibili nella finestra popup sono relative al contesto in cui viene visualizzato il testo. Ad esempio, se si dispone di un'entità basata su apprendimento automatico a 5 livelli e si seleziona il testo al terzo livello (indicato da un nome di entità con etichetta sotto l'espressione di esempio), le entità disponibili nella finestra popup sono limitate al contesto dei sottocomponenti del terzo livello (sottocomponenti di quarto livello). 

## <a name="review-labeled-text"></a>Esaminare il testo etichettato

Dopo l'assegnazione dell'etichetta, rivedere l'espressione di esempio. LUIS applica il modello corrente all'espressione di esempio dopo l'assegnazione dell'etichetta. La linea continua indica che il testo è stato etichettato. 

> [!div class="mx-imgBorder"]
> ![Entità completa basata su apprendimento automatico con etichetta](media/label-utterances/example-1-label-machine-learned-entity-complete-order-labeled.png)

## <a name="when-to-train"></a>Quando eseguire il training

Se il modello corrente deve supportare l'entità etichettata, ma l'espressione di esempio continua a mostrare il testo come stimato ma senza etichetta, eseguire il training dell'app.  

## <a name="confirm-predicted-entity"></a>Confermare l'entità stimata

Se l'indicatore visivo è sopra l'espressione, significa che il testo è stato stimato ma _non ancora etichettato_. Per attivare la stima in un'etichetta, selezionare l'espressione, quindi **Confirm entity predictions** (Conferma stime entità).

> [!div class="mx-imgBorder"]
> ![Stimare un'entità completa basata su apprendimento automatico](media/label-utterances/example-1-label-machine-learned-entity-complete-order-predicted.png)

## <a name="label-subcomponent-entity-by-painting-with-entity-palette-cursor"></a>Etichettare un'entità sottocomponente mediante disegno con il cursore della tavolozza delle entità

1. Per correggere le stime (entità visualizzate sopra l'espressione di esempio), aprire la tavolozza delle entità. 

    > [!div class="mx-imgBorder"]
    > ![Tavolozza delle entità per un'entità con apprendimento automatico](media/label-utterances/pizza-entity-palette-with-pizza-type-selected.png)

1. Selezionare il sottocomponente entità. Questa azione è indicata visivamente con un nuovo cursore. Il cursore segue il mouse durante la navigazione nel portale. 

    > [!div class="mx-imgBorder"]
    > ![Tavolozza delle entità per un'entità con apprendimento automatico](media/label-utterances/pizza-type-entity-palette-cursor.png)

1. Nell'espressione di esempio, _dipingere_ l'entità con il cursore. 

    > [!div class="mx-imgBorder"]
    > ![Tavolozza delle entità per un'entità con apprendimento automatico](media/label-utterances/paint-subcomponent-with-entity-palette-cursor.png)

## <a name="labeling-matching-text-entities-to-a-machine-learned-entity"></a>Assegnazione di etichette alle entità con testo corrispondente a un'entità con apprendimento automatico

Le entità con testo corrispondente includono entità predefinite, entità di espressioni regolari ed entità di elenco. È possibile aggiungerle a un'entità basata su apprendimento automatico, ad esempio i vincoli a un sottocomponente, quando si crea o si modifica l'entità basata su apprendimento automatico. 

**Una volta aggiunti questi vincoli, non è necessario etichettare il testo corrispondente nell'espressione di esempio.**

## <a name="entity-prediction-errors"></a>Errori di stima delle entità

Gli errori di stima delle entità mostrano un indicatore di attenzione. Indica che l'entità stimata non corrisponde all'entità etichettata. 

> [!div class="mx-imgBorder"]
> ![Tavolozza delle entità per un'entità con apprendimento automatico](media/label-utterances/example-utterance-indicates-prediction-error.png)

## <a name="next-steps"></a>Passaggi successivi

Usare il [dashboard](luis-how-to-use-dashboard.md) e [rivedere le espressioni di endpoint](luis-how-to-review-endpoint-utterances.md) per migliorare la qualità della stima di un'app.
