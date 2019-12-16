---
title: 'Esercitazione: Entità elenco - LUIS'
titleSuffix: Azure Cognitive Services
description: Ottenere i dati che corrisponde a un elenco predefinito di elementi. Ogni elemento nell'elenco può avere anche sinonimi che corrispondono esattamente
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: diberry
ms.openlocfilehash: 83644ac7144adaa664b13b6e7d7993c50da0e779
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852470"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance-with-list-entity"></a>Esercitazione: Ottenere dati con corrispondenza esatta di testo da un'espressione con l'entità elenco

Questa esercitazione spiega come ottenere dati con corrispondenza esatta rispetto a un elenco predefinito di elementi.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importare l'app e usare la finalità esistente
> * Aggiungere un'entità elenco
> * Eseguire il training, la pubblicazione e le query sull'app per ottenere i dati estratti

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Cos'è un'entità elenco?

L'entità elenco è una corrispondenza di testo esatta con le parole nell'espressione. Ogni elemento nell'elenco può includere un elenco di sinonimi. Usare un'entità elenco quando si vuole ottenere una corrispondenza esatta.

Per questa applicazione per pizze importata, creare un'entità elenco per i vari tipi di impasto della pizza.

Un'entità elenco è una scelta appropriata per questo tipo di dati quando:

* I valori dei dati sono un set noto.
* Il set non supera i [limiti](luis-boundaries.md) massimi di LUIS per questo tipo di entità.
* Il testo nell'espressione è una corrispondenza esatta con un sinonimo o il nome canonico. LUIS non usa l'elenco di là delle corrispondenze esatte del testo. Stemming, plurali e altre varianti non vengono risolti semplicemente con un'entità elenco. Per gestire le variazioni, è consigliabile usare un [criterio](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) con la sintassi del testo facoltativo.

> [!CAUTION]
> Se non si è certi di volere un'entità elenco o un'entità di Machine Learning con un elenco di frasi come descrittore, la procedura migliore e più flessibile prevede l'uso di un'entità di Machine Learning con un elenco di frasi come descrittore. Questo metodo consente a LUIS di apprendere ed estendere i valori dei dati da estrarre.

## <a name="import-example-json-and-add-utterances"></a>Importare il file JSON di esempio e aggiungere espressioni

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

    [!INCLUDE [Import app steps](includes/import-app-steps.md)]

1. L'app importata presenta una finalità `OrderPizza`. Selezionare tale finalità e aggiungere alcune espressioni con i nuovi tipi di crosta:

    |Nuove espressioni|
    |--|--|
    |ordinare una pizza al salame piccante piccola in teglia|
    |3 pizze hawaiane con impasto sottile|
    |consegnare 2 calzoni con grissini|
    |una pizza con impasto alto da asporto|
    |un trancio di pizza alta al salame piccante|

## <a name="crust-list-entity"></a>Entità elenco per gli impasti della pizza

Ora che la finalità **OrderPizza** include espressioni di esempio con tipi di impasto della pizza, LUIS deve comprendere quali parole rappresentano i tipi di impasto.

Esempi di nome e sinonimi primari sono:

|Nome canonico|Sinonimi|
|--|--|
|Pizza alta al trancio|alto<br>impasto alto al trancio<br>alto<br>impasto alto|
|Trancio|normale<br>originale<br>normale<br>impasto normale<br>impasto originale<br>impasto normale|
|Calzone|calzone|
|Sottile|impasto sottile<br>sottile<br>impasto sottile|

1. Selezionare **Entities** (Entità) nel pannello di sinistra.

1. Selezionare **[+] Create** ([+] Crea).

1. Nella finestra popup relativa all'entità immettere `CrustList` come nome dell'entità e **List** come tipo di entità. Selezionare **Avanti**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot della finestra di dialogo popup di creazione di una nuova entità](media/luis-quickstart-intent-and-list-entity/create-pizza-crust-list-entity.png)

1. Nella pagina **Create a list entity** (Crea un'entità elenco) immettere i nomi canonici e i sinonimi per ogni nome canonico, quindi selezionare **Create** (Crea).

    > [!div class="mx-imgBorder"]
    > ![Screenshot dell'aggiunta di elementi all'entità elenco](media/luis-quickstart-intent-and-list-entity/add-pizza-crust-items-list-entity.png)

    Quando si aggiunge un'entità elenco a un'app LUIS, non è necessario [assegnare un'etichetta](label-entity-example-utterance.md) al testo con l'entità elenco. Viene applicata a tutte le espressioni in tutte le finalità.

## <a name="train-the-app-before-testing-or-publishing"></a>Eseguire il training dell'app prima del test o della pubblicazione

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Pubblicare l'app per eseguire query dall'endpoint

[!INCLUDE [LUIS How to Publish steps](includes/howto-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Ottenere la stima di finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

2. Andare alla fine dell'URL nell'indirizzo e immettere l'espressione seguente:

    `Deliver 2 deep dish hawaiian pizzas and a thin pepperoni`

    L'ultimo parametro querystring è `query`, la **query** dell'espressione.


    ```json
    {
        "query": "Deliver 2 deep dish hawaiian pizzas and a thin pepperoni",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.9957229
                },
                "None": {
                    "score": 0.016832687
                },
                "Confirm": {
                    "score": 0.0015708931
                },
                "Greeting": {
                    "score": 0.00057060417
                }
            },
            "entities": {
                "number": [
                    2
                ],
                "CrustList": [
                    [
                        "Deep dish"
                    ],
                    [
                        "Thin"
                    ]
                ],
                "$instance": {
                    "number": [
                        {
                            "type": "builtin.number",
                            "text": "2",
                            "startIndex": 8,
                            "length": 1,
                            "modelTypeId": 2,
                            "modelType": "Prebuilt Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ],
                    "CrustList": [
                        {
                            "type": "CrustList",
                            "text": "deep dish",
                            "startIndex": 10,
                            "length": 9,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        },
                        {
                            "type": "CrustList",
                            "text": "thin",
                            "startIndex": 42,
                            "length": 4,
                            "modelTypeId": 5,
                            "modelType": "List Entity Extractor",
                            "recognitionSources": [
                                "model"
                            ]
                        }
                    ]
                }
            }
        }
    }
    ```

    I tipi di impasto sono stati trovati come corrispondenze esatte del testo e restituiti nella risposta JSON. Queste informazioni vengono usate dall'applicazione client per elaborare l'ordine.

[!INCLUDE [LUIS How to clean up resources](includes/quickstart-tutorial-cleanup-resources.md)]

## <a name="related-information"></a>Informazioni correlate

* Informazioni concettuali sull'[entità elenco](luis-concept-entity-types.md#list-entity)
* [Come eseguire il training](luis-how-to-train.md)
* [Come eseguire la pubblicazione](luis-how-to-publish-app.md)
* [Come testare nel portale LUIS](luis-interactive-test.md)
* [Concetto - entità](luis-concept-entity-types.md)
* [Informazioni di riferimento JSON sull'entità di espressione regolarei](reference-entity-regular-expression.md?tabs=V3)
* [Come aggiungere le entità per estrarre i dati](luis-how-to-add-entities.md)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono state aggiunte espressioni di esempio e quindi è stato creato un elenco di entità per estrarre corrispondenze esatte del testo dalle espressioni. Dopo il training e la pubblicazione dell'app, una query per l'endpoint ha identificato la finalità e restituito i dati estratti.

> [!div class="nextstepaction"]
> [Aggiungere un'entità predefinita con un ruolo](tutorial-entity-roles.md)

