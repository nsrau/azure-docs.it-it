---
title: "Esercitazione 3: dati corrispondenti a un'espressione regolare - estrarre dati con formattazione corretta"
titleSuffix: Azure Cognitive Services
description: Estrarre dati formattati coerentemente da un'espressione usando l'entità di espressione regolare.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 82d7e5ab57d9cf12c6917386282182faacb07725
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51282391"
---
# <a name="tutorial-3-extract-well-formatted-data"></a>Esercitazione 3: estrarre dati con formattazione corretta
Questa esercitazione illustra come modificare l'app Human Resources per estrarre dati formattati in modo coerente da un'espressione usando l'entità **Espressione regolare**.

Lo scopo di un'entità è estrarre i dati importanti contenuti all'interno dell'espressione. Questa app usa l'entità di espressione regolare per estrarre numeri di modulo formattati delle Risorse Umane (HR) da un'espressione. Mentre la finalità dell'espressione è sempre stabilita tramite Machine Learning, ciò non vale per questo tipo di entità specifico. 

**Le espressioni di esempio includono:**

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
Un'espressione regolare è una scelta appropriata per questo tipo di dati quando:

* i dati sono formattati correttamente.

**In questa esercitazione si apprenderà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usare l'app di esercitazione esistente
> * Aggiungere la finalità FindForm
> * Aggiungere un'entità di espressione regolare 
> * Eseguire il training
> * Pubblica
> * Ottenere finalità ed entità dall'endpoint

[!INCLUDE[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usare l'app esistente
Continuare con l'app creata nell'ultima esercitazione denominata **HumanResources**. 

Se non si dispone dell'app HumanResources dell'esercitazione precedente, usare la procedura seguente:

1. Scaricare e salvare il [file JSON dell'app](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-prebuilts-HumanResources.json).

2. Importare il file JSON in una nuova app.

3. Nella scheda **Versioni** della sezione **Gestisci**, clonare la versione e denominarla `regex`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL. 

## <a name="findform-intent"></a>Finalità FindForm

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selezionare **Create new intent** (Crea nuova finalità). 

3. Immettere `FindForm` nella finestra di dialogo popup, quindi selezionare **Done** (Fine). 

    ![Schermata della finestra di dialogo di creazione della nuova finalità con Utilities (Utilità) nella casella di ricerca](./media/luis-quickstart-intents-regex-entity/create-new-intent-ddl.png)

4. Aggiungere espressioni di esempio alla finalità.

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

    L'applicazione ha un'entità numero predefinita aggiunta con l'esercitazione precedente, quindi ogni numero di modulo è contrassegnato. Ciò può essere sufficiente per l'applicazione client, ma il numero non sarà etichettato con il tipo di numero. La creazione di una nuova entità con un nome appropriato consente all'applicazione client di elaborare correttamente l'entità quando viene restituita da Language Understanding.

    [!INCLUDE[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="regular-expression-entity"></a>Entità di espressione regolare 
L'entità di espressione regolare per individuare il numero di modulo è `hrf-[0-9]{6}`. Questa espressione regolare individua i caratteri letterali `hrf-` ma ignora le varianti di maiuscole/minuscole e di cultura. Individua le cifre 0-9, esattamente per 6 cifre.

HRF è l'acronimo di `human resources form`.

Language Understanding suddivide l'espressione in token quando questa viene aggiunta a una finalità. La suddivisione in token per queste espressioni aggiunge spazi prima e dopo il trattino, `Where is HRF - 123456?`. L'espressione regolare viene applicata all'espressione nella forma non elaborata, prima della suddivisione in token. Dato che viene applicata alla forma _non elaborata_, l'espressione regolare non deve gestire i confini di parola. 

Creare un'entità di espressione regolare per indicare a Language Understanding cos'è un formato di numero HRF seguendo questa procedura:

1. Selezionare **Entities** (Entità) nel pannello di sinistra.

2. Selezionare il pulsante **Create new entity** (Crea nuova entità) nella pagina Entities (Entità). 

3. Nella finestra di dialogo popup, immettere il nome della nuova entità `HRF-number`, selezionare **RegEx** come tipo di entità, immettere `hrf-[0-9]{6}` come valore **Regex**, quindi selezionare  **Done** (Fine).

    ![Schermata della finestra di dialogo popup per l'impostazione delle proprietà della nuova entità](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. Selezionare **Intents** (Finalità) dal menu a sinistra, quindi la finalità **FindForm** per visualizzare l'espressione regolare etichettata nelle espressioni. 

    [![Schermata di etichettatura delle espressioni con l'entità esistente e il modello di espressione regolare](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Poiché l'entità non è un'entità di Machine Learning, l'etichetta viene applicata alle espressioni e visualizzata nel sito Web di Language Understanding non appena viene creata.

## <a name="train"></a>Eseguire il training

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Pubblica

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Ottenere finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Andare alla fine dell'URL nell'indirizzo e immettere `When were HRF-123456 and hrf-234567 published in the last year?`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire la finalità `FindForm` con i due numeri di modulo `HRF-123456` e `hrf-234567`.

    ```JSON
    {
      "query": "When were HRF-123456 and hrf-234567 published in the last year?",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9993477
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9993477
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0206110049
        },
        {
          "intent": "GetJobInformation",
          "score": 0.00533067342
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.004215215
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00209096959
        },
        {
          "intent": "None",
          "score": 0.0017655947
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00109490135
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.0005704638
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.000525338168
        }
      ],
      "entities": [
        {
          "entity": "last year",
          "type": "builtin.datetimeV2.daterange",
          "startIndex": 53,
          "endIndex": 61,
          "resolution": {
            "values": [
              {
                "timex": "2017",
                "type": "daterange",
                "start": "2017-01-01",
                "end": "2018-01-01"
              }
            ]
          }
        },
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
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        },
        {
          "entity": "-234567",
          "type": "builtin.number",
          "startIndex": 28,
          "endIndex": 34,
          "resolution": {
            "value": "-234567"
          }
        }
      ]
    }
    ```

    I numeri nell'espressione vengono restituiti due volte, una come nuova entità `hrf-number` e una come entità predefinita `number`. Un'espressione può avere più di un'entità e più di un tipo di entità, come illustrato in questo esempio. Usando un'entità di espressione regolare, Language Understanding estrae dati denominati, risultato più utile a livello di codice per l'applicazione client che riceve la risposta JSON.


## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stata creata una nuova finalità, sono state aggiunte espressioni di esempio, quindi è stata creata un'entità Espressione regolare per estrarre dati formattati correttamente dalle espressioni. Dopo il training e la pubblicazione dell'app, una query per l'endpoint ha identificato la finalità e restituito i dati estratti.

> [!div class="nextstepaction"]
> [Informazioni sull'entità di elenco](luis-quickstart-intent-and-list-entity.md)

