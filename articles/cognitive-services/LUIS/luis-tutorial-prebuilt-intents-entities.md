---
title: Finalità ed entità predefinite
titleSuffix: Azure Cognitive Services
description: Aggiungere finalità ed entità predefinite all'app dell'esercitazione relativa alle risorse umane per ottenere rapidamente le previsioni delle finalità e l'estrazione dei dati. Non è necessario etichettare ogni espressione con entità predefinite. L'entità viene rilevata automaticamente.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: b6fb603b84cdcf3cb0f75d0020fa2047a0a838d1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074077"
---
# <a name="tutorial-2-identify-common-intents-and-entities"></a>Esercitazione 2: Identificare finalità ed entità comuni
Questa esercitazione illustra come modificare l'app delle risorse umane. Aggiungere finalità ed entità predefinite all'app dell'esercitazione relativa alle risorse umane per ottenere rapidamente le previsioni delle finalità e l'estrazione dei dati. Non è necessario etichettare ogni espressione con entità predefinite perché l'entità viene rilevata automaticamente.

I modelli predefiniti di tipi di domini soggetto e di dati comuni aiutano a creare rapidamente il modello e forniscono un esempio dell'aspetto di un modello analogo. 

**Questa esercitazione apprenderà come:**

> [!div class="checklist"]
> * Usare l'app di esercitazione esistente
> * Aggiungere finalità predefinite 
> * Aggiungere entità predefinite 
> * Eseguire il training 
> * Pubblica 
> * Ottenere finalità ed entità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usare l'app esistente
Continuare con l'app creata nell'ultima esercitazione denominata **HumanResources**. 

Se non si dispone dell'app HumanResources dell'esercitazione precedente, usare la procedura seguente:

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-intent-only-HumanResources.json).

2. Importare il file JSON in una nuova app.

3. Nella scheda **Versioni** della sezione **Gestisci**, clonare la versione e denominarla `prebuilts`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL. 

## <a name="add-prebuilt-intents"></a>Aggiungere finalità predefinite
LUIS fornisce diverse finalità predefinite per le intenzioni comuni degli utenti.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selezionare **Add prebuilt intent** (Aggiungere finalità predefinita). 

3. Cercare `Utilities`. 

    [![Screenshot della finestra di dialogo delle finalità predefinite con Utilities (Utilità) nella casella di ricerca](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. Selezionare le finalità seguenti e quindi scegliere **Done** (Fine): 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>Aggiungere entità predefinite
LUIS fornisce varie entità predefinite per l'estrazione di dati comuni. 

1. Scegliere **Entities** (Entità) dal menu di spostamento a sinistra.

2. Selezionare **Manage prebuilt entity** (Gestisci entità predefinita).

3. Selezionare **number** e **datetimeV2** nell'elenco delle entità predefinite e quindi scegliere **Done** (Fine).

    ![Screenshot dell'entità number selezionata nella finestra di dialogo relativa alle entità predefinite](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train"></a>Eseguire il training

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Pubblica

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Ottenere finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Andare alla fine dell'URL nella barra degli indirizzi del browser e immettere `I want to cancel on March 3`. L'ultimo parametro della stringa di query è `q`, la **query** dell'espressione. 

    ```json
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

    Il risultato ha previsto la finalità Utilities.Cancel e ha estratto la data del 3 marzo e il numero 3. 

    Sono presenti due valori per il 3 marzo, perché l'espressione non ha indicato se il 3 marzo è relativo al passato o al futuro. L'applicazione client deve farà una supposizione o richiederà chiarimenti, se necessario. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

Aggiungendo finalità ed entità predefinite, l'applicazione client può stabilire intenzioni dell'utente comuni ed estrarre i datatype comuni. 

> [!div class="nextstepaction"]
> [Aggiungere un'entità di espressione regolare all'app](luis-quickstart-intents-regex-entity.md)

