---
title: 'Esercitazione 9: analisi delle valutazioni positive, negative e neutre in LUIS'
titleSuffix: Azure Cognitive Services
description: Questa esercitazione mostra come creare un'app che illustra come estrarre una valutazione positiva, negativa e neutra da espressioni. La valutazione è determinata dall'intera espressione.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 60c4b280033e110f6b8b2a3ce720934e118c8479
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424851"
---
# <a name="tutorial-9--extract-sentiment-of-overall-utterance"></a>Esercitazione 9: estrazione della valutazione dell'intera espressione
Questa esercitazione mostra come creare un'app che illustra come estrarre una valutazione positiva, negativa e neutra da espressioni. La valutazione è determinata dall'intera espressione.

L'analisi del sentiment offre la possibilità di determinare se un'espressione utente è positiva, negativa o neutra. 

Le espressioni seguenti mostrano alcuni esempi di valutazione:

|Valutazione|Score|Espressione|
|:--|:--|:--|
|positivo|0,91 |John W. Smith did a great job on the presentation in Paris.|
|positivo|0,84 |jill-jones@mycompany.com did fabulous work on the Parker sales pitch.|

L'analisi della valutazione è un'impostazione dell'app che si applica a ogni espressione. Non è necessario trovare le parole che indicano il sentiment nell'espressione ed etichettarle, perché l'analisi del sentiment si applica all'intera espressione. 

Poiché si tratta di un'impostazione di pubblicazione, non è presente nelle pagine di finalità o entità. È possibile visualizzarla nel riquadro [test interattivo](luis-interactive-test.md#view-sentiment-results) o durante l'esecuzione del test nell'URL dell'endpoint. 

**In questa esercitazione si apprenderà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usare l'app di esercitazione esistente 
> * Aggiungere l'analisi della valutazione come impostazione di pubblicazione
> * Eseguire il training
> * Pubblica
> * Ottenere la valutazione dell'espressione dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usare l'app esistente

Continuare con l'app creata nell'ultima esercitazione denominata **HumanResources**. 

Se non si dispone dell'app HumanResources dell'esercitazione precedente, usare la procedura seguente:

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-keyphrase-HumanResources.json).

2. Importare il file JSON in una nuova app.

3. Nella scheda **Versioni** della sezione **Gestisci**, clonare la versione e denominarla `sentiment`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL.

## <a name="employeefeedback-intent"></a>Finalità EmployeeFeedback 
Aggiungere una nuova finalità per acquisire il feedback dei dipendenti dai membri dell'azienda. 

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selezionare **Create new intent** (Crea nuova finalità).

3. Immettere il nome della nuova finalità `EmployeeFeedback`.

    ![Finestra di dialogo per la creazione di una nuova finalità denominata EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Aggiungere diverse espressioni che indicano un dipendente che fa qualcosa di positivo o un'area che richiede un miglioramento:

    Tenere presente in questa app relativa alle risorse umane i dipendenti sono definiti nell'entità elenco, `Employee`, tramite il nome, l'indirizzo di posta elettronica, il numero di interno, il numero di telefono cellulare e il codice fiscale federale degli Stati Uniti. 

    |Espressioni|
    |--|
    |425-555-1212 did a nice job of welcoming back a co-worker from maternity leave|
    |234-56-7891 did a great job of comforting a co-worker in their time of grief.|
    |jill-jones@mycompany.com didn't have all the required invoices for the paperwork.|
    |john.w.smith@mycompany.com turned in the required forms a month late with no signatures|
    |x23456 didn't make it to the important marketing off-site meeting.|
    |x12345 missed the meeting for June reviews.|
    |Jill Jones rocked the sales pitch at Harvard|
    |John W. Smith did a great job on the presentation at Stanford|

    [ ![Screenshot dell'app LUIS con espressioni di esempio nella finalità EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="train"></a>Eseguire il training

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurare un'app per includere l'analisi del sentiment
1. Selezionare **Gestisci** nella barra di spostamento in alto a destra, quindi selezionare **Impostazioni di pubblicazione** nel menu a sinistra.

2. Attivare **Analisi del sentiment** per abilitare questa impostazione. 

    ![](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish"></a>Pubblica

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-sentiment-of-utterance-from-endpoint"></a>Ottenere la valutazione dell'espressione dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Andare alla fine dell'URL nell'indirizzo e immettere `Jill Jones work with the media team on the public portal was amazing`. L'ultimo parametro QueryString è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni con etichetta, per cui rappresenta un buon test e deve restituire la finalità `EmployeeFeedback` con analisi del sentiment estratta.
    
    ```JSON
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.4983256
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.4983256
        },
        {
          "intent": "MoveEmployee",
          "score": 0.06617523
        },
        {
          "intent": "GetJobInformation",
          "score": 0.04631853
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0103248553
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.007531875
        },
        {
          "intent": "FindForm",
          "score": 0.00344597152
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00337914471
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0026357458
        },
        {
          "intent": "None",
          "score": 0.00214573368
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00157622492
        },
        {
          "intent": "Utilities.Confirm",
          "score": 7.379545E-05
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 0,
          "endIndex": 9,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "media team",
          "type": "builtin.keyPhrase",
          "startIndex": 25,
          "endIndex": 34
        },
        {
          "entity": "public portal",
          "type": "builtin.keyPhrase",
          "startIndex": 43,
          "endIndex": 55
        },
        {
          "entity": "jill jones",
          "type": "builtin.keyPhrase",
          "startIndex": 0,
          "endIndex": 9
        }
      ],
      "sentimentAnalysis": {
        "label": "positive",
        "score": 0.8694164
      }
    }
    ```

    Il valore di sentimentAnalysis è positivo con un punteggio pari a 0,86. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione sono state aggiunte le analisi della valutazione come impostazione di pubblicazione al fine di estrarre i valori della valutazione dall'espressione nel suo complesso.

> [!div class="nextstepaction"] 
> [Esaminare le espressioni endpoint nell'app relativa alle risorse umane](luis-tutorial-review-endpoint-utterances.md) 

