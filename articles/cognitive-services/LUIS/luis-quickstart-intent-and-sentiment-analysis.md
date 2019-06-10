---
title: Analisi del sentiment
titleSuffix: Azure Cognitive Services
description: Questa esercitazione mostra come creare un'app che illustra come ottenere una valutazione positiva, negativa e neutra da espressioni. La valutazione è determinata dall'intera espressione.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 06/03/2019
ms.author: diberry
ms.openlocfilehash: 1408e29793fdac77b89e3f0cc0a7be525f7fa1d2
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66479796"
---
# <a name="tutorial--get-sentiment-of-utterance"></a>Esercitazione:  Ottenere la valutazione dell'espressione

Questa esercitazione mostra come creare un'app che illustra come determinare una valutazione positiva, negativa e neutra da espressioni. La valutazione è determinata dall'intera espressione.

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare una nuova app
> * Aggiungere l'analisi della valutazione come impostazione di pubblicazione
> * Eseguire il training dell'app
> * Pubblicare l'app
> * Ottenere la valutazione dell'espressione dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="sentiment-analysis-is-a-publish-setting"></a>L'analisi della valutazione è un'impostazione di pubblicazione

Le espressioni seguenti mostrano alcuni esempi di valutazione:

|Valutazione|Score|Espressione|
|:--|:--|:--|
|positivo|0,91 |John W. Smith did a great job on the presentation in Paris.|
|positivo|0,84 |The Seattle engineers did fabulous work on the Parker sales pitch.|

L'analisi della valutazione è un'impostazione dell'app che si applica a ogni espressione. Non è necessario trovare le parole che indicano una valutazione nell'espressione e contrassegnarle. 

Poiché si tratta di un'impostazione di pubblicazione, non è presente nelle pagine di finalità o entità. È possibile visualizzarla nel riquadro [test interattivo](luis-interactive-test.md#view-sentiment-results) o durante l'esecuzione del test nell'URL dell'endpoint. 


## <a name="create-a-new-app"></a>Creare una nuova app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="add-personname-prebuilt-entity"></a>Aggiungere l'entità predefinita PersonName 

1. Selezionare **Build** (Compila) nel menu di spostamento.

1. Scegliere **Entities** (Entità) dal menu di spostamento a sinistra.

1. Selezionare il pulsante **Add prebuilt entity** (Aggiungi entità predefinite).

1. Selezionare l'entità seguente dall'elenco delle entità predefinite e quindi selezionare **Done** (Fine):

   * **[PersonName](luis-reference-prebuilt-person.md)** 

     ![Screenshot dell'entità number selezionata nella finestra di dialogo relativa alle entità predefinite](./media/luis-quickstart-intent-and-sentiment-analysis/add-personname-prebuilt-entity.png)

## <a name="create-an-intent-to-determine-employee-feedback"></a>Creare una finalità per determinare il feedback dei dipendenti

Aggiungere una nuova finalità per acquisire il feedback dei dipendenti dai membri dell'azienda. 

1. Selezionare **Intents** (Finalità) dal pannello di sinistra.

1. Selezionare **Create new intent** (Crea nuova finalità).

1. Immettere il nome della nuova finalità `EmployeeFeedback`.

    ![Finestra di dialogo per la creazione di una nuova finalità denominata EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-create-new-intent-ddl.png)

4. Aggiungere diverse espressioni che indicano un dipendente che fa qualcosa di positivo o un'area che richiede un miglioramento:

    |Espressioni|
    |--|
    |John Smith did a nice job of welcoming back a co-worker from maternity leave|
    |Jill Jones did a great job of comforting a co-worker in her time of grief.|
    |Bob Barnes didn't have all the required invoices for the paperwork.|
    |Todd Thomas turned in the required forms a month late with no signatures|
    |Katherine Kelly didn't make it to the important marketing off-site meeting.|
    |Denise Dillard missed the meeting for June reviews.|
    |Mark Mathews rocked the sales pitch at Harvard|
    |Walter Williams did a great job on the presentation at Stanford|

    Selezionare **View options** (Opzioni di visualizzazione) e quindi **Show entity values** (Mostra valori entità) per visualizzare i nomi.

    [![Screenshot dell'app LUIS con espressioni di esempio nella finalità EmployeeFeedback](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png)](./media/luis-quickstart-intent-and-sentiment-analysis/hr-utterance-examples.png#lightbox)

## <a name="add-example-utterances-to-the-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna) 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Eseguire il training dell'app in modo che sia possibile testare la finalità 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="configure-app-to-include-sentiment-analysis"></a>Configurare un'app per includere l'analisi del sentiment

1. Selezionare **Gestisci** nella barra di spostamento in alto a destra, quindi selezionare **Impostazioni di pubblicazione** nel menu a sinistra.

1. Selezionare **Use sentiment analysis to determine if a user's utterance is positive, negative, or neutral** (Usa analisi del sentiment per determinare se un'espressione dell'utente è positiva, negativa o neutra) per abilitare questa impostazione. 

    ![Attivare l'analisi della valutazione come impostazione di pubblicazione](./media/luis-quickstart-intent-and-sentiment-analysis/turn-on-sentiment-analysis-as-publish-setting.png)

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Pubblicare l'app in modo che sia possibile eseguire query dall'endpoint sul modello con training

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-the-sentiment-of-an-utterance-from-the-endpoint"></a>Ottenere la valutazione di un'espressione dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Andare alla fine dell'URL nell'indirizzo e immettere l'espressione seguente:

    `Jill Jones work with the media team on the public portal was amazing` 

    L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni con etichetta, per cui rappresenta un buon test e deve restituire la finalità `EmployeeFeedback` con analisi del sentiment estratta.
    
    ```json
    {
      "query": "Jill Jones work with the media team on the public portal was amazing",
      "topScoringIntent": {
        "intent": "EmployeeFeedback",
        "score": 0.9616192
      },
      "intents": [
        {
          "intent": "EmployeeFeedback",
          "score": 0.9616192
        },
        {
          "intent": "None",
          "score": 0.09347677
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
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

    Il valore di sentimentAnalysis è positivo con un punteggio pari all'86%. 

    Provare un'altra espressione rimuovendo il valore di `q` nella barra degli indirizzi del browser: `William Jones did a terrible job presenting his ideas.` Il punteggio del sentiment indica un sentiment negativo restituendo un punteggio basso pari a `0.18597582`.

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

