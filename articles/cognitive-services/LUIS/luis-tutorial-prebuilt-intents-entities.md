---
title: 'Esercitazione: Finalità ed entità predefinite - LUIS'
titleSuffix: Azure Cognitive Services
description: In questa esercitazione, aggiungere finalità ed entità predefinite a un'app per ottenere rapidamente le previsioni delle finalità e l'estrazione dei dati. Non è necessario etichettare ogni espressione con entità predefinite. L'entità viene rilevata automaticamente.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: diberry
ms.openlocfilehash: 0a59d9783eac122f96b1671f2dba5d0d708e1d83
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73499387"
---
# <a name="tutorial-identify-common-intents-and-entities"></a>Esercitazione: Identificare finalità ed entità comuni

In questa esercitazione, aggiungere finalità ed entità predefinite a un'app dell'esercitazione relativa alle risorse umane per ottenere rapidamente le previsioni delle finalità e l'estrazione dei dati. Non è necessario contrassegnare ogni espressione con entità predefinite perché l'entità viene rilevata automaticamente.

I modelli predefiniti (domini, finalità ed entità) aiutano a compilare il modello rapidamente.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

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

1. Selezionare tutte le finalità e quindi **Done** (Fine). Queste finalità sono utili a determinare in quale punto della conversazione si trova l'utente e cosa sta chiedendo di fare. 

## <a name="add-prebuilt-entities-to-help-with-common-data-type-extraction"></a>Aggiungere entità predefinite per aiutare nell'estrazione del tipo di dati comune

LUIS fornisce varie entità predefinite per l'estrazione di dati comuni. 

1. Scegliere **Entities** (Entità) dal menu di spostamento a sinistra.

1. Selezionare il pulsante **Add prebuilt entity** (Aggiungi entità predefinite).

1. Selezionare le entità seguenti dall'elenco delle entità predefinite e quindi selezionare **Done** (Fine):

   * **[GeographyV2](luis-reference-prebuilt-geographyV2.md)**

     Questa entità consente di aggiungere il riconoscimento della posizione all'applicazione client.

## <a name="add-example-utterances-to-the-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna) 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Eseguire il training dell'app in modo che sia possibile testare la finalità 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Pubblicare l'app in modo che sia possibile eseguire query dall'endpoint sul modello con training

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Ottenere la stima di finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Andare alla fine dell'URL nella barra degli indirizzi del browser e immettere `I want to cancel my trip to Seattle`. L'ultimo parametro della stringa di query è `q`, la **query** dell'espressione. 

    ```json
    {
      "query": "I want to cancel my trip to Seattle",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.1055009
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.1055009
        },
        {
          "intent": "Utilities.SelectItem",
          "score": 0.02659072
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0253379084
        },
        {
          "intent": "Utilities.ReadAloud",
          "score": 0.02528683
        },
        {
          "intent": "Utilities.SelectNone",
          "score": 0.02434013
        },
        {
          "intent": "Utilities.Escalate",
          "score": 0.009161292
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006861785
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00633448
        },
        {
          "intent": "Utilities.ShowNext",
          "score": 0.0053827134
        },
        {
          "intent": "None",
          "score": 0.002602003
        },
        {
          "intent": "Utilities.ShowPrevious",
          "score": 0.001797354
        },
        {
          "intent": "Utilities.SelectAny",
          "score": 0.000831930141
        },
        {
          "intent": "Utilities.Repeat",
          "score": 0.0006924066
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.000606057351
        },
        {
          "intent": "Utilities.GoBack",
          "score": 0.000276725681
        },
        {
          "intent": "Utilities.FinishTask",
          "score": 0.000267822179
        },
        {
          "intent": "Utilities.Reject",
          "score": 3.21784828E-05
        }
      ],
      "entities": [
        {
          "entity": "seattle",
          "type": "builtin.geographyV2.city",
          "startIndex": 28,
          "endIndex": 34
        }
      ]
    }
    ```

    Il risultato ha previsto la finalità Utilities.Cancel con un'affidabilità dell'80% e ha estratto i dati relativi alla città. 


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

