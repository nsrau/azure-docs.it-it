---
title: Finalità ed entità predefinite
titleSuffix: Azure Cognitive Services
description: In questa esercitazione, aggiungere finalità ed entità predefinite a un'app per ottenere rapidamente le previsioni delle finalità e l'estrazione dei dati. Non è necessario etichettare ogni espressione con entità predefinite. L'entità viene rilevata automaticamente.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: cf16f27a8d39871491b7cf46a509b9714a669667
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56873815"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Esercitazione: Identificare finalità ed entità comuni

In questa esercitazione, aggiungere finalità ed entità predefinite a un'app dell'esercitazione relativa alle risorse umane per ottenere rapidamente le previsioni delle finalità e l'estrazione dei dati. Non è necessario contrassegnare ogni espressione con entità predefinite perché l'entità viene rilevata automaticamente.

I modelli predefiniti (domini, finalità ed entità) aiutano a compilare il modello rapidamente.

**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Creare una nuova app
> * Aggiungere finalità predefinite 
> * Aggiungere entità predefinite 
> * Eseguire il training 
> * Pubblica 
> * Ottenere finalità ed entità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Creare una nuova app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]


## <a name="add-prebuilt-intents-to-help-with-common-user-intentions"></a>Aggiungere finalità predefinite per le intenzioni comuni degli utenti

LUIS fornisce diverse finalità predefinite per le intenzioni comuni degli utenti.  

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selezionare **Add prebuilt domain intent** (Aggiungi finalità di dominio predefinita). 

1. Cercare `Utilities`. 

    [![Screenshot della finestra di dialogo delle finalità predefinite con Utilities (Utilità) nella casella di ricerca](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

1. Selezionare le finalità seguenti e quindi scegliere **Done** (Fine): 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop

    Queste finalità sono utili a determinare in quale punto della conversazione si trova l'utente e cosa sta chiedendo di fare. 


## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Aggiungere entità predefinite per aiutare nell'estrazione del tipo di dati comune

LUIS fornisce varie entità predefinite per l'estrazione di dati comuni. 

1. Scegliere **Entities** (Entità) dal menu di spostamento a sinistra.

1. Selezionare il pulsante **Add prebuilt entity** (Aggiungi entità predefinite).

1. Selezionare le entità seguenti dall'elenco delle entità predefinite e quindi selezionare **Done** (Fine):

    * **[PersonName](luis-reference-prebuilt-person.md)** 
    * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

    ![Screenshot dell'entità number selezionata nella finestra di dialogo relativa alle entità predefinite](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

    Queste entità consentono di aggiungere il riconoscimento di nome e posizione all'applicazione client.

## <a name="add-example-utterances-to-the-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna) 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Eseguire il training dell'app in modo che sia possibile testare la finalità 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Pubblicare l'app in modo che sia possibile eseguire query dall'endpoint sul modello con training

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Ottenere la stima di finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Andare alla fine dell'URL nella barra degli indirizzi del browser e immettere `I want to cancel my trip to Seattle to see Bob Smith`. L'ultimo parametro della stringa di query è `q`, la **query** dell'espressione. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle to see Bob Smith",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.807676256
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.807676256
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0487322025
        },
        {
          "intent": "Utilities.Help",
          "score": 0.0208660364
        },
        {
          "intent": "None",
          "score": 0.008789532
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.006929268
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00136293867
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        },
        {
          "entity": "bob smith",
          "type": "builtin.personName",
          "startIndex": 43,
          "endIndex": 51
        }
      ]
    }
    ```

    Il risultato ha previsto la finalità Utilities.Cancel con una sicurezza dell'80% e ha estratto i dati relativi a città e nome della persona. 


## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informazioni correlate

Altre informazioni sui modelli predefiniti:

* [Domini predefiniti](luis-reference-prebuilt-domains.md): si tratta dei domini comuni che riducono le operazioni generali di creazione dell'app LUIS
* Finalità predefinite: si tratta delle finalità singole dei domini comuni. È possibile aggiungere le finalità singolarmente anziché aggiungere l'intero dominio.
* [Entità predefinite](luis-prebuilt-entities.md): si tratta di tipi di dati comuni utili per la maggior parte delle app LUIS.

Altre informazioni sull'uso dell'app LUIS:

* [Come eseguire il training](luis-how-to-train.md)
* [Come eseguire la pubblicazione](luis-how-to-publish-app.md)
* [Come testare nel portale LUIS](luis-interactive-test.md)

## <a name="next-steps"></a>Passaggi successivi

Aggiungendo finalità ed entità predefinite, l'applicazione client può stabilire intenzioni dell'utente comuni ed estrarre i datatype comuni.  

> [!div class="nextstepaction"]
> [Aggiungere un'entità di espressione regolare all'app](luis-quickstart-intents-regex-entity.md)

