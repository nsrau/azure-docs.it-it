---
title: Dati contestuali con ruoli - LUIS
titleSuffix: Azure Cognitive Services
description: Trovare dati correlati basati sul contesto. Ad esempio, spostare una località di origine e destinazione di un computer fisico da un edificio e un ufficio a un altro edificio e ufficio sono informazioni correlate.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 2eac05712dc7b3deceba52681195101f9bf2b40c
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560004"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Esercitazione: Estrarre dati con una relazione di tipo contestuale da un'espressione

Questa esercitazione illustra come trovare informazioni correlate di dati basati sul contesto. Ad esempio, una posizione di origine e una di destinazione per il trasferimento da una città a un'altra. Potrebbero essere necessari entrambi i dati, che sono correlati tra loro.  

Un ruolo può essere usato con qualsiasi tipo di entità predefinita o personalizzata, e può essere usato sia nelle espressioni che nei modelli di esempio. 

**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Creare una nuova app
> * Aggiungere le finalità 
> * Ottenere le informazioni di origine e destinazione usando i ruoli
> * Eseguire il training
> * Pubblica
> * Ottenere finalità e ruoli di entità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="related-data"></a>Dati correlati

Questa app determina dove deve essere trasferito un dipendente, dalla città di origine alla città di destinazione. Usa l'entità predefinita GeographyV2 per identificare i nomi delle città e usa i ruoli per determinare i tipi di località (origine e destinazione) all'interno dell'espressione.

Un ruolo deve essere usato quando i dati dell'entità da estrarre:

* Sono correlati tra loro nel contesto dell'espressione.
* Usano parole specifiche per indicare ogni ruolo. Le parole usate sono, ad esempio, from/to, leaving/headed to, away from/toward.
* Entrambi i ruoli si trovano spesso nella stessa espressione, consentendo a LUIS di imparare da questo uso contestuale frequente.
* Devono essere raggruppati ed elaborati dall'applicazione client come un'unità di informazioni.

## <a name="create-a-new-app"></a>Creare una nuova app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Creare una finalità per spostare i dipendenti tra città

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selezionare **Create new intent** (Crea nuova finalità). 

1. Immettere `MoveEmployeeToCity` nella finestra di dialogo popup, quindi selezionare **Done** (Fine). 

    ![Screenshot della finestra di dialogo di creazione di una nuova finalità](./media/tutorial-entity-roles/create-new-intent-move-employee-to-city.png)

1. Aggiungere espressioni di esempio alla finalità.

    |Espressioni di esempio|
    |--|
    |move John W. Smith leaving Seattle headed to Orlando|
    |transfer Jill Jones from Seattle to Cairo|
    |Place John Jackson away from Tampa, coming to Atlanta |
    |move Debra Doughtery to Tulsa from Chicago|
    |mv Jill Jones leaving Cairo headed to Tampa|
    |Shift Alice Anderson to Oakland from Redmond|
    |Carl Chamerlin from San Francisco to Redmond|
    |Transfer Steve Standish from San Diego toward Bellevue |
    |lift Tanner Thompson from Kansas city and shift to Chicago|

    [![Screenshot di LUIS con nuove espressioni nella finalità MoveEmployee](./media/tutorial-entity-roles/hr-enter-utterances.png)](./media/tutorial-entity-roles/hr-enter-utterances.png#lightbox)

## <a name="add-prebuilt-entity-geographyv2"></a>Aggiungere l'entità predefinita geographyV2

L'entità predefinita geographyV2 estrae le informazioni sulla posizione, compresi i nomi delle città. Poiché le espressioni hanno due nomi di città, correlati tra loro nel contesto, usare i ruoli per estrarre tale contesto.

1. Selezionare **Entities** (Entità) nel riquadro di spostamento a sinistra.

1. Selezionare **Add prebuilt entity** (Aggiungi entità predefinita), quindi selezionare `geo` nella barra di ricerca per filtrare le entità predefinite. 

    ![Aggiungere l'entità predefinita geographyV2 all'app](media/tutorial-entity-roles/add-geographyV2-prebuilt-entity.png)
1. Selezionare la casella di controllo e selezionare **Done** (Fine).
1. Nell'elenco **Entities** (Entità) selezionare **geographyV2** per aprire la nuova entità. 
1. Aggiungere i due ruoli `Origin` e `Destination`. 

    ![Aggiungere i ruoli all'entità predefinita](media/tutorial-entity-roles/add-roles-to-prebuilt-entity.png)
1. Selezionare **Intents** (Finalità) nel riquadro di spostamento a sinistra, quindi selezionare la finalità **MoveEmployeeToCity**. Si noti che i nomi delle città sono etichettati con l'entità predefinita **geographyV2**.
1. Nella prima espressione dell'elenco selezionare la posizione di origine. Viene visualizzato un menu a discesa. Selezionare **geographyV2** nell'elenco, quindi seguire il menu per selezionare **Origin** (Origine).
1. Usare il metodo del passaggio precedente per contrassegnare tutti i ruoli delle posizioni in tutte le espressioni. 


## <a name="add-example-utterances-to-the-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna) 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Eseguire il training dell'app in modo che sia possibile testare la finalità 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Pubblicare l'app in modo che sia possibile eseguire query dall'endpoint sul modello con training

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Ottenere la stima di finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Passare alla fine dell'URL nella barra degli indirizzi e immettere `Please move Carl Chamerlin from Tampa to Portland`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire la finalità `MoveEmployee` con l'entità estratta.

    ```json
    {
      "query": "Please move Carl Chamerlin from Tampa to Portland",
      "topScoringIntent": {
        "intent": "MoveEmployeeToCity",
        "score": 0.979823351
      },
      "intents": [
        {
          "intent": "MoveEmployeeToCity",
          "score": 0.979823351
        },
        {
          "intent": "None",
          "score": 0.0156363435
        }
      ],
      "entities": [
        {
          "entity": "geographyV2",
          "role": "Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "geographyV2",
          "role": "Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    Viene stimata la finalità corretta e la matrice delle entità mostra sia il ruolo di origine sia il ruolo di destinazione nelle proprietà **entity** corrispondenti.
    
## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informazioni correlate

* [Concetti di entità](luis-concept-entity-types.md)
* [Concetti di ruolo](luis-concept-roles.md)
* [Elenco di entità predefinite](luis-reference-prebuilt-entities.md)
* [Come eseguire il training](luis-how-to-train.md)
* [Come eseguire la pubblicazione](luis-how-to-publish-app.md)
* [Come testare nel portale LUIS](luis-interactive-test.md)
* [Ruoli](luis-concept-roles.md)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una nuova finalità e sono state aggiunte espressioni di esempio per i dati acquisiti in base al contesto relativi alla posizione di origine e di destinazione. Una volta eseguiti il training e la pubblicazione dell'app, un'applicazione client può usare tali informazioni per creare un ticket di spostamento con le informazioni pertinenti.

> [!div class="nextstepaction"] 
> [Informazioni su come aggiungere un'entità composta](luis-tutorial-composite-entity.md) 
