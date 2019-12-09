---
title: 'Esercitazione: Impostazioni di pubblicazione - LUIS'
titleSuffix: Azure Cognitive Services
description: In questa esercitazione verranno cambiate le impostazioni di pubblicazione per migliorare le previsioni.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/03/2019
ms.author: diberry
ms.openlocfilehash: 0e105d70ff7f590a84e0a82c15bcdd83052b63a8
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2019
ms.locfileid: "74808004"
---
# <a name="tutorial--add-sentiment-analysis-as-a-publishing-setting"></a>Esercitazione:  Aggiungere l'analisi del sentiment come impostazione di pubblicazione

In questa esercitazione vengono modificate le impostazioni di pubblicazione per estrarre l'analisi del sentiment e quindi viene eseguita una query sull'endpoint LUIS per vedere il sentiment restituito da un'espressione dell'utente.

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Aggiungere l'analisi del sentiment come impostazione di pubblicazione
> * Ottenere il sentiment di un'espressione dall'endpoint pubblicato

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>L'analisi della valutazione è un'impostazione di pubblicazione

Le espressioni seguenti mostrano alcuni esempi di valutazione:

|Valutazione|Score|Espressione|
|:--|:--|:--|
|negativo|0.01 |La pizza era orribile.|
|positivo|0,97 |La pizza al formaggio era meravigliosa.|

L'analisi della valutazione è un'impostazione dell'app che si applica a ogni espressione. Una volta impostata, l'app restituisce il sentiment di un'espressione senza la necessità di etichettare i dati.

Poiché si tratta di un'impostazione di pubblicazione, non è etichettata nelle pagine di finalità o entità. È possibile visualizzarla nel riquadro [test interattivo](luis-interactive-test.md#view-sentiment-results) o durante l'esecuzione del test nell'URL dell'endpoint.

## <a name="import-example-json-to-begin-app"></a>Importare il file con estensione json per iniziare l'app

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/machine-learned-entity/pizza-tutorial-with-entities.json).

[!INCLUDE [Import app steps](includes/import-app-steps.md)]

## <a name="train-the-app"></a>Eseguire il training dell'app

[!INCLUDE [LUIS How to Train steps](includes/howto-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurare un'app per includere l'analisi del sentiment

1. Scegliere **Publish** (Pubblica) dal menu in alto. L'analisi del sentiment è un'impostazione di pubblicazione.

1. Selezionare **Slot di produzione** e quindi **Cambia impostazioni**.
1. Impostare l'impostazione Analisi del sentiment su **Attivata**.

    ![Attivare l'analisi della valutazione come impostazione di pubblicazione](./media/luis-quickstart-intent-and-sentiment-analysis/select-sentiment-publishing-setting.png)

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Ottenere la valutazione di un'espressione dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](includes/howto-get-endpoint.md)]

1. Andare alla fine dell'URL nell'indirizzo e immettere l'espressione seguente:

    `Deliver 2 of the best cheese pizzas ever!!!`

    L'ultimo parametro querystring è `query`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni con etichetta, per cui rappresenta un buon test e deve restituire la finalità `OrderPizza` con analisi del sentiment estratta.

    ```json
    {
        "query": "Deliver 2 of the best cheese pizzas ever!!!",
        "prediction": {
            "topIntent": "OrderPizza",
            "intents": {
                "OrderPizza": {
                    "score": 0.4659952
                },
                "None": {
                    "score": 0.16901511
                },
                "Confirm": {
                    "score": 0.00421415
                },
                "Greeting": {
                    "score": 0.00109914376
                }
            },
            "entities": {
                "number": [
                    2
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
                    ]
                }
            },
            "sentiment": {
                "label": "positive",
                "score": 0.861665964
            }
        }
    }
    ```

    L'analisi del sentiment restituisce un valore positivo con un punteggio dell'86%.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informazioni correlate

* L'analisi del sentiment è fornita dal servizio cognitivo [Analisi del testo](../Text-Analytics/index.yml). La funzionalità è limitata alle [lingue supportate](luis-language-support.md##languages-supported) da Analisi del testo.
* [Come eseguire il training](luis-how-to-train.md)
* [Come eseguire la pubblicazione](luis-how-to-publish-app.md)
* [Come testare nel portale LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono state aggiunte le analisi della valutazione come impostazione di pubblicazione al fine di estrarre i valori della valutazione dall'espressione nel suo complesso.

> [!div class="nextstepaction"]
> [Esaminare le espressioni endpoint nell'app relativa alle risorse umane](luis-tutorial-review-endpoint-utterances.md)

