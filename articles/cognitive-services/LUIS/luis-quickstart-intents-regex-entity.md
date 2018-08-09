---
title: "Esercitazione: Creazione di un'app di Language Understanding per ottenere dati di corrispondenza di espressioni regolari - Azure | Microsoft Docs"
description: Questa esercitazione illustra come creare una semplice app di Language Understanding usando finalità e un'entità di espressione regolare per estrarre dati.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 994bd6f2a041e25d15c7e0b4a216952cec4101fa
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39492824"
---
# <a name="tutorial-3-add-regular-expression-entity"></a>Esercitazione: 3. Aggiungere un'entità di espressione regolare
Questa esercitazione illustra come creare un'app che dimostra come estrarre dati formattati in modo coerente da un'espressione usando l'entità di **espressione regolare**.


<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendere le entità di espressione regolare 
> * Usare un'app di Language Understanding per il settore delle risorse umane (HR) con la finalità FindForm
> * Aggiungere un'entità di espressione regolare per estrarre il numero di modulo dall'espressione
> * Eseguire il training e pubblicare l'app
> * Eseguire query sull'endpoint dell'app per ottenere una risposta JSON di Language Understanding

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Prima di iniziare
Se non è disponibile l'app relativa alle risorse umane descritta nell'esercitazione sulle [entità predefinite](luis-tutorial-prebuilt-intents-entities.md), [importare](luis-how-to-start-new-app.md#import-new-app) il codice JSON in una nuova app nel sito Web [LUIS](luis-reference-regions.md#luis-website) dal repository GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-prebuilts-HumanResources.json).

Se si vuole mantenere l'app originale, clonare la versione nella pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) (Impostazioni) assegnando il nome `regex`. La clonazione è un ottimo modo per provare le diverse funzionalità di Language Understanding senza modificare la versione originale. 


## <a name="purpose-of-the-regular-expression-entity"></a>Scopo dell'entità di espressione regolare
Lo scopo di un'entità è ottenere i dati importanti contenuti nell'espressione. L'app usa l'entità di espressione regolare per estrarre numeri di modulo formattati delle Risorse Umane (HR) da un'espressione. L'operazione non si basa su Machine Learning. 

Espressioni di esempio semplici includono:

```
Where is HRF-123456?
Who authored HRF-123234?
HRF-456098 is published in French?
```

Le versioni abbreviate o gergali delle espressioni includono:

```
HRF-456098
HRF-456098 date?
HRF-456098 title?
```
 
L'entità di espressione regolare per individuare il numero di modulo è `hrf-[0-9]{6}`. Questa espressione regolare individua i caratteri letterali `hrf -` ma ignora le varianti di maiuscole/minuscole e di cultura. Individua le cifre 0-9, esattamente per 6 cifre.

HRF sta per "modulo risorse umane".

### <a name="tokenization-with-hyphens"></a>Tokenizzazione con trattini
Language Understanding suddivide l'espressione in token quando l'espressione viene aggiunta a una finalità. La suddivisione in token per queste espressioni aggiunge spazi prima e dopo il trattino, `Where is HRF - 123456?`. L'espressione regolare viene applicata all'espressione nella forma non elaborata, prima della suddivisione in token. Dato che viene applicata alla forma _non elaborata_, l'espressione regolare non deve gestire i confini di parola. 


## <a name="add-findform-intent"></a>Aggiungere la finalità FindForm

1. Assicurarsi che l'app Risorse umane sia presente nella sezione **Build** di Language Understanding. È possibile passare a questa sezione selezionando **Build** (Compila) nella barra dei menu in alto a destra. 

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

## <a name="create-an-hrf-number-regular-expression-entity"></a>Creare un'entità di espressione regolare per i numeri HRF 
Creare un'entità di espressione regolare per indicare a Language Understanding cos'è un formato di numero HRF seguendo questa procedura:

1. Selezionare **Entities** (Entità) nel pannello di sinistra.

2. Selezionare il pulsante **Create new entity** (Crea nuova entità) nella pagina Entities (Entità). 

3. Nella finestra di dialogo popup, immettere il nome della nuova entità `HRF-number`, selezionare **RegEx**  come tipo di entità, immettere `hrf-[0-9]{6}` come espressione regolare, quindi selezionare **Done**  (Fine).

    ![Schermata della finestra di dialogo popup per l'impostazione delle proprietà della nuova entità](./media/luis-quickstart-intents-regex-entity/create-regex-entity.png)

4. Selezionare **Intents** (Finalità) e quindi la finalità **FindForm** per visualizzare l'espressione regolare etichettata nelle espressioni. 

    [![Schermata di etichettatura delle espressioni con l'entità esistente e il modello di espressione regolare](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png)](./media/luis-quickstart-intents-regex-entity/labeled-utterances-for-entity.png#lightbox)

    Poiché l'entità non è un'entità di Machine Learning, l'etichetta viene applicata alle espressioni e visualizzata nel sito Web di Language Understanding non appena viene creata.

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>Eseguire una query nell'endpoint con un'espressione diversa

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Andare alla fine dell'URL nell'indirizzo e immettere `When were HRF-123456 and hrf-234567 published in the last year?`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire la finalità `FindForm` con i due numeri di modulo `HRF-123456` e `hrf-234567`.

    ```
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

## <a name="what-has-this-luis-app-accomplished"></a>Quali attività ha eseguito l'app di Language Understanding?
Questa app identificato la finalità e restituito i dati estratti. 

Il chatbot ha ora informazioni sufficienti per determinare l'azione principale, `FindForm`, e quali numeri di modulo erano nella ricerca. 

## <a name="where-is-this-luis-data-used"></a>Qual è la destinazione d'uso dei dati di Language Understanding? 
Language Understanding ha completato le attività relative alla richiesta. L'applicazione chiamante, ad esempio un chatbot, può acquisire il risultato topScoringIntent e i numeri di modulo ed eseguire ricerche in un'API di terze parti. Language Understanding non esegue queste operazioni, ma determina solo l'intenzione dell'utente ed estrae i dati relativi a tale intenzione. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'entità di elenco](luis-quickstart-intent-and-list-entity.md)

