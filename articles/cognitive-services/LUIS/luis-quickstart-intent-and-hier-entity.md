---
title: Entità gerarchica
titleSuffix: Azure Cognitive Services
description: Trovare informazioni correlate di dati basati sul contesto. Ad esempio, spostare una località di origine e destinazione di un computer fisico da un edificio e un ufficio a un altro edificio e ufficio sono informazioni correlate.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: dc8b9831b89aade7ca5dfd3e1f53b9dccc15e66b
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55217034"
---
# <a name="tutorial-extract-contextually-related-data-from-an-utterance"></a>Esercitazione: Estrarre dati con una relazione di tipo contestuale da un'espressione

Questa esercitazione illustra come trovare informazioni correlate di dati basati sul contesto. Ad esempio, una posizione di origine e una di destinazione per il trasferimento da una città a un'altra. Potrebbero essere necessari entrambi i dati, che sono correlati tra loro.  

**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Creare una nuova app
> * Aggiungere le finalità 
> * Aggiungere un'entità gerarchica di località con elementi figlio di origine e destinazione
> * Eseguire il training
> * Pubblica
> * Ottenere finalità ed entità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="hierarchical-data"></a>Dati gerarchici

Questa app determina dove deve essere trasferito un dipendente, dalla città di origine alla città di destinazione. Viene usata l'entità gerarchica per determinare le posizioni nell'espressione. 

L'entità gerarchica è una buona soluzione per questo tipo di dati, perché i due dati, le posizioni figlio:

* Sono entità semplici.
* Sono correlati tra loro nel contesto dell'espressione.
* Usano parole specifiche per indicare ogni entità. Le parole usate sono, ad esempio, from/to, leaving/headed to, away from/toward.
* I due elementi figlio si trovano spesso nella stessa espressione. 
* Devono essere raggruppati ed elaborati dall'applicazione client come un'unità di informazioni.

## <a name="create-a-new-app"></a>Creare una nuova app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-move-employees-between-cities"></a>Creare una finalità per spostare i dipendenti tra città

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Selezionare **Create new intent** (Crea nuova finalità). 

1. Immettere `MoveEmployeeToCity` nella finestra di dialogo popup, quindi selezionare **Done** (Fine). 

    ![Screenshot della finestra di dialogo di creazione di una nuova finalità](./media/luis-quickstart-intent-and-hier-entity/create-new-intent-move-employee-to-city.png)

1. Aggiungere espressioni di esempio alla finalità.

    |Espressioni di esempio|
    |--|
    |move John W. Smith leaving Seattle headed to Dallas|
    |transfer Jill Jones from Seattle to Cairo|
    |Place John Jackson away from Tampa, coming to Atlanta |
    |move Debra Doughtery to Tulsa from Dallas|
    |mv Jill Jones leaving Cairo headed to Tampa|
    |Shift Alice Anderson to Oakland from Redmond|
    |Carl Chamerlin from San Francisco to Redmond|
    |Transfer Steve Standish from San Diego toward Bellevue |
    |lift Tanner Thompson from Kansas city and shift to Chicago|

    [ ![Screenshot di LUIS con nuove espressioni nella finalità MoveEmployee](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png)](./media/luis-quickstart-intent-and-hier-entity/hr-enter-utterances.png#lightbox)

## <a name="create-a-location-entity"></a>Creare un'entità posizione
LUIS deve comprendere le posizioni tramite etichette per l'origine e la destinazione nelle espressioni. Se è necessario visualizzare l'espressione nella vista token (non elaborata), selezionare l'interruttore **Entities View** (Vista entità) sulla barra sopra le espressioni. Dopo aver spostato l'interruttore, l'etichetta del controllo diventerà **Tokens View** (Vista token).

Si consideri l'espressione seguente:

```json
move John W. Smith leaving Seattle headed to Dallas
```

Nell'espressione sono specificate due posizioni, `Seattle` e `Dallas`. Entrambi i valori sono raggruppati come figli di un'entità gerarchica, `Location`, perché è necessario estrarre entrambi i dati dall'espressione per completare la richiesta nell'applicazione client e i dati sono correlati tra loro. 
 
Se è presente un solo elemento figlio (di origine o di destinazione) di un'entità gerarchica, verrà comunque estratto. Non è necessario individuare tutti gli elementi figlio per estrarne solo uno o alcuni. 

1. Nell'espressione `move John W. Smith leaving Seattle headed to Dallas` selezionare la parola `Seattle`. Verrà visualizzato un menu a discesa con una casella di testo nella parte superiore. Immettere il nome dell'entità `Location` nella casella di testo, quindi selezionare **Create new entity** (Crea nuova entità) nel menu a discesa. 

    [![Screenshot della creazione di una nuova entità nella pagina delle finalità](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png "Screenshot della creazione di una nuova entità nella pagina delle finalità")](media/luis-quickstart-intent-and-hier-entity/create-location-hierarchical-entity-from-example-utterance.png#lightbox)

1. Nella finestra popup, selezionare il tipo di entità **Hierarchical** (Gerarchica) con `Origin` e `Destination` come entità figlio. Selezionare **Operazione completata**.

    ![Screenshot della finestra di dialogo popup di creazione di entità per la nuova entità di posizione](media/luis-quickstart-intent-and-hier-entity/hr-create-new-entity-2.png "Screenshot della finestra di dialogo popup di creazione di entità per la nuova entità di posizione")

1. L'etichetta per `Seattle` è contrassegnata come `Location` perché Language Understanding non sa se il termine era l'origine, la destinazione o nessuna delle due. Selezionare `Seattle`, quindi selezionare **Location** (Posizioni) e infine selezionare `Origin` dal menu a destra.

    [![Screenshot della finestra di dialogo delle etichette di entità per modificare gli elementi figlio dell'entità Locations (Posizioni)](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png "Screenshot della finestra di dialogo delle etichette di entità per modificare gli elementi figlio dell'entità Locations (Posizioni)")](media/luis-quickstart-intent-and-hier-entity/choose-hierarchical-child-entity-from-example-utterance.png#lightbox)

1. Assegnare un'etichetta alle altre località in tutte le altre espressioni. Una volta etichettate tutte le posizioni, l'aspetto delle espressioni diventa simile a un modello. 

    [![Screenshot dell'entità Locations (Posizioni) con etichetta nelle espressioni](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png "Screenshot dell'entità Locations (Posizioni) con etichetta nelle espressioni")](media/luis-quickstart-intent-and-hier-entity/all-intents-marked-with-origin-and-destination-location.png#lightbox)

    La sottolineatura rossa indica che LUIS non è certo dell'entità. Il training risolve il problema. 

## <a name="add-example-utterances-to-the-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna) 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Eseguire il training dell'app in modo che sia possibile testare la finalità 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Pubblicare l'app in modo che sia possibile eseguire query dall'endpoint sul modello con training

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Ottenere la stima di finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]


1. Passare alla fine dell'URL nella barra degli indirizzi e immettere `Please move Carl Chamerlin from Tampa to Portland`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, pertanto rappresenta un buon test e dovrebbe restituire la finalità `MoveEmployee` con l'entità gerarchica estratta.

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
          "entity": "portland",
          "type": "Location::Destination",
          "startIndex": 41,
          "endIndex": 48,
          "score": 0.6044041
        },
        {
          "entity": "tampa",
          "type": "Location::Origin",
          "startIndex": 32,
          "endIndex": 36,
          "score": 0.739491045
        }
      ]
    }
    ```
    
    Viene stimata la finalità corretta e la matrice delle entità mostra sia il valore di origine sia il valore di destinazione nelle proprietà **entità** corrispondenti.
    
## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informazioni correlate

* Informazioni concettuali sulle [entità gerarchiche](luis-concept-entity-types.md)
* [Come eseguire il training](luis-how-to-train.md)
* [Come eseguire la pubblicazione](luis-how-to-publish-app.md)
* [Come testare nel portale LUIS](luis-interactive-test.md)
* [Ruoli ed entità gerarchiche](luis-concept-roles.md#roles-versus-hierarchical-entities)
* [Migliorare l'accuratezza della stima con i criteri](luis-concept-patterns.md)

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata una nuova finalità e sono state aggiunte espressioni di esempio per i dati acquisiti in base al contesto relativi alla posizione di origine e di destinazione. Una volta eseguiti il training e la pubblicazione dell'app, un'applicazione client può usare tali informazioni per creare un ticket di spostamento con le informazioni pertinenti.

> [!div class="nextstepaction"] 
> [Informazioni su come aggiungere un'entità composta](luis-tutorial-composite-entity.md) 
