---
title: Esercitazione sulle entità composite-LUIS
titleSuffix: Azure Cognitive Services
description: Aggiungere un'entità composita per aggregare i dati estratti di vario tipo in una singola entità contenitore. Aggregando i dati, l'applicazione client può estrarre facilmente i dati correlati in diversi tipi di dati.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 02/19/2019
ms.author: diberry
ms.openlocfilehash: 6d54b46ff8b0c8a987653b4fc0344377c0115b58
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68560093"
---
# <a name="tutorial-group-and-extract-related-data"></a>Esercitazione: Raggruppare ed estrarre dati correlati
Questa esercitazione illusta come aggiungere un'entità composita per aggregare i dati estratti di vario tipo in una singola entità contenitore. Aggregando i dati, l'applicazione client può estrarre facilmente i dati correlati in diversi tipi di dati.

Lo scopo dell'entità composita è di raggruppare le entità correlate in un'entità di categoria padre. Prima che venga creata un'entità composita le informazioni sono entità separate. 

L'entità composta è una buona soluzione per questo tipo di dati, perché i dati:

* Sono correlati tra loro. 
* Usano vari tipi di entità.
* Devono essere raggruppati ed elaborati dall'applicazione client come un'unità di informazioni.

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importare l'app di esempio
> * Creare finalità
> * Aggiungere un'entità composita 
> * Eseguire il training
> * Pubblica
> * Ottenere finalità ed entità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importare l'app di esempio

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/tutorial_list.json) dall'esercitazione sull'entità List (Elenco).

2. Importare il file JSON in una nuova app.

3. Nella scheda **Versioni** della sezione **Gestisci**, clonare la versione e denominarla `composite`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL.

## <a name="composite-entity"></a>Entità composita

In questa app, il nome del reparto è definito nell'entità elenco **Department** e include sinonimi. 

La finalità **TransferEmployeeToDepartment** contiene espressioni di esempio per richiedere lo spostamento di un dipendente in un nuovo reparto. 

Le espressioni di esempio per questa finalità includono:

|Espressioni di esempio|
|--|
|move John W. Smith to the accounting department|
|transfer Jill Jones from to R&D|
 
La richiesta di spostamento deve includere il nome del reparto e il nome del dipendente. 

## <a name="add-the-personname-prebuilt-entity-to-help-with-common-data-type-extraction"></a>Aggiungere l'entità predefinita PersonName come supporto per l'estrazione del tipo di dati comune

LUIS fornisce varie entità predefinite per l'estrazione di dati comuni. 

1. Selezionare **Build** (Compila) dalla barra di spostamento in alto, quindi selezionare **Intents** (Finalità) dal menu di spostamento a sinistra.

1. Selezionare **Manage prebuilt entity** (Gestisci entità predefinita).

1. Selezionare **[PersonName](luis-reference-prebuilt-person.md)** nell'elenco di entità predefinite e quindi **Done** (Fine).

    ![Screenshot dell'entità number selezionata nella finestra di dialogo relativa alle entità predefinite](./media/luis-tutorial-composite-entity/add-personname-prebuilt-entity.png)

    Questa entità consente di aggiungere il riconoscimento dei nomi all'applicazione client.

## <a name="create-composite-entity-from-example-utterances"></a>Creare un'entità composita da espressioni di esempio

1. Selezionare **Intents** (Finalità) nel pannello di spostamento sinistro.

1. Selezionare **TransferEmployeeToDepartment** nell'elenco di finalità.

1. Nell'espressione `place John Jackson in engineering`Selezionare l'entità PersonName, `John Jackson`quindi selezionare **Wrap nell'entità composita** nell'elenco dei menu popup per l'espressione seguente. 

    ![Screenshot della selezione del wrapping composito nella finestra di dialogo a discesa](./media/luis-tutorial-composite-entity/hr-create-composite-entity-1.png)

1. Quindi selezionare immediatamente l'ultima entità, `engineering`, nell'espressione. Sotto le parole selezionate viene disegnata una barra verde che indica un'entità composita. Nel menu a comparsa, immettere il nome composto `TransferEmployeeInfo` quindi premere INVIO. 

    ![Screenshot dell'immissione del nome composito nella finestra di dialogo a discesa](./media/luis-tutorial-composite-entity/hr-create-composite-entity-2.png)

1. In **What type of entity do you want to create** (Che tipo di entità si vuole creare?) tutti i campi necessari sono nell'elenco: `personName` e `Department`. Selezionare **Operazione completata**. Si noti che l'entità predefinita personName è stata aggiunta all'entità composita. Se si può disporre di un'entità predefinita che viene visualizzata tra il token di apertura e il token di chiusura di un'entità composta, l'entità composta deve contenere tali entità predefinite. Se le entità predefinite non sono incluse, l'entità composta non sarà stimata correttamente e la stima sarà eseguita per ogni singolo elemento.

    ![Screenshot dell'immissione del nome composito nella finestra di dialogo a discesa](./media/luis-tutorial-composite-entity/hr-create-composite-entity-3.png)

## <a name="label-example-utterances-with-composite-entity"></a>Etichettare le espressioni di esempio con l'entità composita

1. In ogni espressione di esempio, selezionare l'entità più a sinistra nel composito. Selezionare quindi **Wrap in composite entity** (Esegui il wrapping in entità composita).

1. Selezionare l'ultima parola nell'entità composita e quindi scegliere **TransferEmployeeInfo** dal menu di scelta rapida. 

1. Verificare che tutte le espressioni nella finalità siano etichettate con l'entità composita. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Eseguire il training dell'app in modo che sia possibile testare la finalità 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Pubblicare l'app in modo che sia possibile eseguire query dall'endpoint sul modello con training

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Ottenere la stima di finalità ed entità dall'endpoint 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Andare alla fine dell'URL nell'indirizzo e immettere `Move Jill Jones to DevOps`. L'ultimo parametro querystring è `q`, la query dell'espressione. 

    Poiché questo test consiste nel verificare che il composito venga estratto in modo corretto, il test può includere un'espressione di esempio esistente o una nuova espressione. Un buon test consiste nell'includere tutte le entità figlio nell'entità composita.

    ```json
    {
      "query": "Move Jill Jones to DevOps",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9882747
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9882747
        },
        {
          "intent": "None",
          "score": 0.00925369747
        }
      ],
      "entities": [
        {
          "entity": "jill jones",
          "type": "builtin.personName",
          "startIndex": 5,
          "endIndex": 14
        },
        {
          "entity": "devops",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 24,
          "resolution": {
            "values": [
              "Development Operations"
            ]
          }
        },
        {
          "entity": "jill jones to devops",
          "type": "TransferEmployeeInfo",
          "startIndex": 5,
          "endIndex": 24,
          "score": 0.9607566
        }
      ],
      "compositeEntities": [
        {
          "parentType": "TransferEmployeeInfo",
          "value": "jill jones to devops",
          "children": [
            {
              "type": "builtin.personName",
              "value": "jill jones"
            },
            {
              "type": "Department",
              "value": "devops"
            }
          ]
        }
      ]
    }
    ```

   Questa espressione restituisce una matrice di entità composite. A ogni entità viene assegnato un tipo e un valore. Per individuare con maggiore precisione ogni entità figlio, usare la combinazione di tipo e valore dell'elemento di matrice composita per trovare l'elemento corrispondente nella matrice delle entità.  

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informazioni correlate

* [Esercitazione per l'entità List (Elenco)](luis-quickstart-intents-only.md)
* Informazioni concettuali sull'[entità composita](luis-concept-entity-types.md)
* [Come eseguire il training](luis-how-to-train.md)
* [Come eseguire la pubblicazione](luis-how-to-publish-app.md)
* [Come testare nel portale LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'entità composta per incapsulare le entità esistenti. In questo modo l'applicazione client trova un gruppo di dati correlati in diversi tipi di dati per continuare la conversazione. Un'applicazione client per questa app Human Resources potrebbe richiedere di specificare il giorno e l'ora in cui lo spostamento deve iniziare e terminare. Potrebbe richiedere anche altre informazioni sullo spostamento, come un numero di telefono fisico. 

> [!div class="nextstepaction"] 
> [Informazioni su come aggiungere un'entità semplice con un elenco di frasi](luis-quickstart-primary-and-secondary-data.md)  
