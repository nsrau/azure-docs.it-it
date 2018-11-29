---
title: 'Esercitazione 8: estrazione di frase chiave in LUIS'
titleSuffix: Azure Cognitive Services
description: Usare l'entità KeyPhrase predefinita per estrarre domini chiave da espressioni. Non è necessario etichettare ogni espressione con entità predefinite. L'entità viene rilevata automaticamente.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: fafb6a6cb986b5bcd795c9bac0fadfce2827c667
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52424813"
---
# <a name="tutorial-8-extract-key-phrases-of-utterance"></a>Esercitazione 8: estrarre frasi chiave di espressione
In questa esercitazione viene usata l'entità KeyPhrase predefinita per estrarre domini chiave da espressioni. Non è necessario etichettare ogni espressione con entità predefinite. L'entità viene rilevata automaticamente.

Le espressioni seguenti mostrano alcuni esempi di frasi chiave:

|Espressione|Valori di entità KeyPhrase|
|--|--|
|Per il prossimo anno è disponibile un nuovo piano sanitario a una franchigia inferiore?|"franchigia inferiore"<br>"nuovo piano sanitario"<br>"anno"|
|Il visual training è coperto dal piano sanitario con franchigia elevata?|"piano sanitario con franchigia elevata"<br>"visual training"|

L'applicazione client può usare questi valori, insieme ad altre entità estratte, per stabilire il passaggio successivo nella conversazione.

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usare l'app di esercitazione esistente
> * Aggiungere entità KeyPhrase 
> * Eseguire il training
> * Pubblica
> * Ottenere finalità ed entità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usare l'app esistente

Continuare con l'app creata nell'ultima esercitazione denominata **HumanResources**. 

Se non si dispone dell'app HumanResources dell'esercitazione precedente, usare la procedura seguente:

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-simple-HumanResources.json).

2. Importare il file JSON in una nuova app.

3. Nella scheda **Versioni** della sezione **Gestisci**, clonare la versione e denominarla `keyphrase`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL.

## <a name="add-keyphrase-entity"></a>Aggiungere entità KeyPhrase 
Aggiungere entità KeyPhrase predefinite per estrarre domini da espressioni.

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selezionare **Entità** nel menu a sinistra.

3. Selezionare **Add prebuilt entity** (Aggiungi entità predefinite).

4. Nella finestra di dialogo popup, selezionare **KeyPhrase**, quindi selezionare **Operazione completata**. 

    [ ![Screenshot della finestra di dialogo popup Elenco di entità](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/hr-add-or-remove-prebuilt-entities.png#lightbox)

5. Scegliere **Intents** (Finalità) dal menu a sinistra e quindi scegliere la finalità **Utilities.Confirm**. L'entità KeyPhrase è etichettata in diverse espressioni. 

    [ ![Screenshot della finalità Utilities.Confirm con l'entità KeyPhrase etichettata nelle espressioni](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png)](./media/luis-quickstart-intent-and-key-phrase/hr-keyphrase-labeled.png#lightbox)

## <a name="train"></a>Eseguire il training

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Pubblica

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Ottenere finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Andare alla fine dell'URL nell'indirizzo e immettere `does form hrf-123456 cover the new dental benefits and medical plan`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. 
    
    ```JSON
    {
      "query": "does form hrf-123456 cover the new dental benefits and medical plan",
      "topScoringIntent": {
        "intent": "FindForm",
        "score": 0.9300641
      },
      "intents": [
        {
          "intent": "FindForm",
          "score": 0.9300641
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0359598845
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0141798034
        },
        {
          "intent": "MoveEmployee",
          "score": 0.0112197418
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00507669244
        },
        {
          "intent": "None",
          "score": 0.00238501839
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00202810857
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00102957746
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.0008688423
        },
        {
          "intent": "Utilities.Confirm",
          "score": 3.557994E-05
        }
      ],
      "entities": [
        {
          "entity": "hrf-123456",
          "type": "HRF-number",git 
          "startIndex": 10,
          "endIndex": 19
        },
        {
          "entity": "new dental benefits",
          "type": "builtin.keyPhrase",
          "startIndex": 31,
          "endIndex": 49
        },
        {
          "entity": "medical plan",
          "type": "builtin.keyPhrase",
          "startIndex": 55,
          "endIndex": 66
        },
        {
          "entity": "hrf",
          "type": "builtin.keyPhrase",
          "startIndex": 10,
          "endIndex": 12
        },
        {
          "entity": "-123456",
          "type": "builtin.number",
          "startIndex": 13,
          "endIndex": 19,
          "resolution": {
            "value": "-123456"
          }
        }
      ]
    }
    ```

    Durante la ricerca per di un modulo, l'utente ha fornito più informazioni del necessario per trovare il modulo. Le informazioni aggiuntive vengono restituite come **builtin.keyPhrase**. L'applicazione client può usare queste informazioni aggiuntive per una domanda di completamento, ad esempio per chiedere se si desidera parlare con un rappresentate del reparto risorse umane in merito ai nuovi vantaggi dentistici, oppure per fornire un menu con altre opzioni, tra cui una relativa ad altre informazioni sui vantaggi dentistici o su un piano medico.

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione ha illustrato come aggiungere l'entità KeyPhrase predefinita, che permette di ottenere rapidamente le frasi chiave in espressioni senza la necessità di etichettare le espressioni stesse. 

> [!div class="nextstepaction"]
> [Aggiungere l'analisi del sentiment all'app](luis-quickstart-intent-and-sentiment-analysis.md)
