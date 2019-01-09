---
title: Entità di espressioni normali
titleSuffix: Azure Cognitive Services
description: Estrarre dati formattati coerentemente da un'espressione usando l'entità di espressione regolare.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 58fa0c36f8c3f630ae7f349bd0f54a497a38f19d
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976784"
---
# <a name="tutorial-get-well-formatted-data-from-the-utterance"></a>Esercitazione: Ottenere dati formattati in modo corretto dall'espressione
Questa esercitazione illustra come creare un'app per estrarre dati formattati in modo coerente da un'espressione usando l'entità di **espressione regolare**.

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare una nuova app 
> * Aggiungere le finalità
> * Aggiungere un'entità di espressione regolare 
> * Eseguire il training
> * Pubblica
> * Ottenere finalità ed entità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="regular-expression-entities"></a>Entità di espressione regolare

Questa app usa l'entità di espressione regolare per estrarre numeri di modulo formattati correttamente delle Risorse Umane (HR) da un'espressione. Mentre la finalità dell'espressione è sempre stabilita tramite Machine Learning, ciò non vale per questo tipo di entità specifico. 

**Le espressioni di esempio includono:**

|Espressioni di esempio|
|--|
|Dove si trova HRF-123456?|
|Chi ha creato HRF-123234?|
|HRF-456098 viene pubblicato in francese?|
|HRF 456098|
|Data di HRF 456098?|
 
Un'espressione regolare è una scelta appropriata per questo tipo di dati quando:

* i dati sono formattati correttamente.


## <a name="create-a-new-app"></a>Creare una nuova app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-finding-form"></a>Creare finalità per la ricerca del modulo

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selezionare **Create new intent** (Crea nuova finalità). 

1. Immettere `FindForm` nella finestra di dialogo popup, quindi selezionare **Done** (Fine). 

    ![Schermata della finestra di dialogo di creazione della nuova finalità con Utilities (Utilità) nella casella di ricerca](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

1. Aggiungere espressioni di esempio alla finalità.

    |Espressioni di esempio|
    |--|
    |Qual è l'URL di hrf-123456?|
    |Dove si trova hrf-345678?|
    |Quando è stato aggiornato hrf-456098?|
    |Guido Rossi ha aggiornato hrf-234639 la scorsa settimana?|
    |Quante versioni di hrf-345123 ci sono?|
    |Chi deve autorizzare il modulo hrf-123456?|
    |Quante persone devono autorizzare hrf-345678?|
    |Qual è la data di hrf-234123?|
    |Chi è l'autore di hrf-546234?|
    |Qual è il titolo di hrf-456234?|

    [ ![Schermata della pagina della finalità con le nuove espressioni evidenziate](./media/luis-quickstart-intents-regex-entity/findform-intent.png) ](./media/luis-quickstart-intents-regex-entity/findform-intent.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="use-the-regular-expression-entity-for-well-formatted-data"></a>Utilizzare l'entità di espressione regolare per dati formattati in modo corretto
L'entità di espressione regolare per individuare il numero di modulo è `hrf-[0-9]{6}`. Questa espressione regolare individua i caratteri letterali `hrf-` ma ignora le varianti di maiuscole/minuscole e di cultura. Individua le cifre 0-9, esattamente per 6 cifre.

HRF è l'acronimo di `human resources form`.

Language Understanding suddivide l'espressione in token quando questa viene aggiunta a una finalità. La suddivisione in token per queste espressioni aggiunge spazi prima e dopo il trattino, `Where is HRF - 123456?`. L'espressione regolare viene applicata all'espressione nella forma non elaborata, prima della suddivisione in token. Dato che viene applicata alla forma _non elaborata_, l'espressione regolare non deve gestire i confini di parola. 

Creare un'entità di espressione regolare per indicare a Language Understanding cos'è un formato di numero HRF seguendo questa procedura:

1. Selezionare **Entities** (Entità) nel pannello di sinistra.

1. Selezionare il pulsante **Create new entity** (Crea nuova entità) nella pagina Entities (Entità). 

1. Nella finestra di dialogo popup, immettere il nome della nuova entità `HRF-number`, selezionare **RegEx** come tipo di entità, immettere `hrf-[0-9]{6}` come valore **Regex**, quindi selezionare  **Done** (Fine).

    ![Schermata della finestra di dialogo popup per l'impostazione delle proprietà della nuova entità](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

1. Selezionare **Intents** (Finalità) dal menu a sinistra, quindi la finalità **FindForm** per visualizzare l'espressione regolare etichettata nelle espressioni. 

    [![Schermata di etichettatura delle espressioni con l'entità esistente e il modello di espressione regolare](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Poiché l'entità non è un'entità di Machine Learning, l'entità viene applicata alle espressioni e visualizzata nel sito Web di Language Understanding non appena viene creata.

## <a name="add-example-utterances-to-the-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna) 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Eseguire il training dell'app prima del test o della pubblicazione

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Pubblicare l'app per eseguire query dall'endpoint

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Ottenere la stima di finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Andare alla fine dell'URL nell'indirizzo e immettere `When were HRF-123456 and hrf-234567 published in the last year?`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire la finalità `FindForm` con i due numeri di modulo `HRF-123456` e `hrf-234567`.

    ```json
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9988884
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9988884
        },
        {
          "intent": "None",
          "score": 0.00204812363
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "hrf-234567",
          "type": "HRF-number",
          "startIndex": 25,
          "endIndex": 34
        }
      ]
    }
    ```

    Usando un'entità di espressione regolare, Language Understanding estrae dati denominati, risultato più utile a livello di codice per l'applicazione client che riceve la risposta JSON.


## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informazioni correlate

* Concetti sull'entità [espressione regolare](luis-concept-entity-types.md#regular-expression-entity)
* [Come eseguire il training](luis-how-to-train.md)
* [Come eseguire la pubblicazione](luis-how-to-publish-app.md)
* [Come testare nel portale LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stata creata una nuova finalità, sono state aggiunte espressioni di esempio, quindi è stata creata un'entità Espressione regolare per estrarre dati formattati correttamente dalle espressioni. Dopo il training e la pubblicazione dell'app, una query per l'endpoint ha identificato la finalità e restituito i dati estratti.

> [!div class="nextstepaction"]
> [Informazioni sull'entità di elenco](luis-quickstart-intent-and-list-entity.md)

