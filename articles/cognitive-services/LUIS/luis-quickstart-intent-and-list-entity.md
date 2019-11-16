---
title: 'Esercitazione: Corrispondenza di testo esatta - LUIS'
titleSuffix: Azure Cognitive Services
description: Ottenere i dati che corrisponde a un elenco predefinito di elementi. Ogni elemento nell'elenco può avere anche sinonimi che corrispondono esattamente
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: diberry
ms.openlocfilehash: a8021885bf51ab6d44bc8576b9fdd69f1bdd270a
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953676"
---
# <a name="tutorial-get-exact-text-matched-data-from-an-utterance"></a>Esercitazione: Ottenere dati esattamente corrispondenti al testo da un'espressione

Questa esercitazione spiega come ottenere dati entità corrispondenti a un elenco predefinito di elementi. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare l'app
> * Aggiungere le finalità
> * Aggiungere un'entità elenco 
> * Eseguire il training 
> * Pubblica
> * Ottenere finalità ed entità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="what-is-a-list-entity"></a>Cos'è un'entità elenco?

L'entità elenco è una corrispondenza di testo esatta con le parole nell'espressione. 

Ogni elemento nell'elenco può includere un elenco di sinonimi. Per l'app delle risorse umane, un reparto aziendale può essere identificato da diverse informazioni, ad esempio un nome ufficiale, acronimi comuni e i codici di fatturazione del reparto. 

L'app delle risorse umane deve determinare il reparto in cui si sta trasferendo un dipendente. 

Un'entità elenco è una scelta appropriata per questo tipo di dati quando:

* I valori dei dati sono un set noto.
* Il set non supera i [limiti](luis-boundaries.md) massimi di LUIS per questo tipo di entità.
* Il testo nell'espressione è una corrispondenza esatta con un sinonimo o il nome canonico. LUIS non usa l'elenco di là delle corrispondenze esatte del testo. Stemming, plurali e altre varianti non vengono risolti semplicemente con un'entità elenco. Per gestire le variazioni, è consigliabile usare un [criterio](reference-pattern-syntax.md#syntax-to-mark-optional-text-in-a-template-utterance) con la sintassi del testo facoltativo. 

## <a name="create-a-new-app"></a>Creare una nuova app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-an-intent-to-transfer-employees-to-a-different-department"></a>Creare una finalità per trasferire i dipendenti in un reparto diverso

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Selezionare **Create new intent** (Crea nuova finalità). 

3. Immettere `TransferEmployeeToDepartment` nella finestra di dialogo popup, quindi selezionare **Done** (Fine). 

    ![Screenshot della finestra di dialogo di creazione di una nuova finalità](./media/luis-quickstart-intent-and-list-entity/hr-create-new-intent-ddl.png)

4. Aggiungere espressioni di esempio alla finalità.

    |Espressioni di esempio|
    |--|
    |move John W. Smith to the accounting department|
    |transfer Jill Jones from to R&D|
    |Dept 1234 has a new member named Bill Bradstreet|
    |Place John Jackson in Engineering |
    |move Debra Doughtery to Inside Sales|
    |mv Jill Jones to IT|
    |Shift Alice Anderson to DevOps|
    |Carl Chamerlin to Finance|
    |Steve Standish to 1234|
    |Tanner Thompson to 3456|

    [![Screenshot della finalità con espressioni di esempio](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png "Screenshot della finalità con espressioni di esempio")](media/luis-quickstart-intent-and-list-entity/intent-transfer-employee-to-department.png#lightbox)

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]  

## <a name="department-list-entity"></a>Entità elenco reparti

Ora che la finalità **TransferEmployeeToDepartment** ha espressioni di esempio, LUIS deve capire cos'è un reparto. 

Il nome _canonico_ per ogni elemento è il nome del reparto. Ecco alcuni sinonimi di esempio di ogni nome canonico: 

|Nome canonico|Sinonimi|
|--|--|
|Contabilità|acct<br>accting<br>3456|
|Sviluppo e operazioni|DevOps<br>4949|
|Engineering|eng<br>enging<br>4567|
|Finanza|fin<br>2020|
|Reparto IT|IT<br>2323|
|Vendite interne|isale<br>insale<br>1414|
|Ricerca e sviluppo|R&D<br>1234|

1. Selezionare **Entities** (Entità) nel pannello di sinistra.

1. Selezionare **Create new entity** (Crea nuova entità).

1. Nella finestra popup relativa all'entità immettere `Department` come nome dell'entità e **List** come tipo di entità. Selezionare **Operazione completata**.  

    [![Screenshot della finestra popup di creazione di una nuova entità](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png "Screenshot della finestra popup di creazione di una nuova entità")](media/luis-quickstart-intent-and-list-entity/create-new-list-entity-named-department.png#lightbox)

1. Nella pagina relativa all'entità reparto immettere `Accounting` come nuovo valore.

1. Per i sinonimi, aggiungere i sinonimi dalla tabella precedente.

1. Continuare ad aggiungere tutti i nomi canonici e i relativi sinonimi. 

## <a name="add-example-utterances-to-the-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna) 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Eseguire il training dell'app in modo che sia possibile testare la finalità 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Pubblicare l'app in modo che sia possibile eseguire query dall'endpoint sul modello con training

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Ottenere la stima di finalità ed entità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

1. Andare alla fine dell'URL nell'indirizzo e immettere `shift Joe Smith to IT`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire la finalità `TransferEmployeeToDepartment` con l'oggetto `Department` estratto.

   ```json
    {
      "query": "shift Joe Smith to IT",
      "topScoringIntent": {
        "intent": "TransferEmployeeToDepartment",
        "score": 0.9775754
      },
      "intents": [
        {
          "intent": "TransferEmployeeToDepartment",
          "score": 0.9775754
        },
        {
          "intent": "None",
          "score": 0.0154493852
        }
      ],
      "entities": [
        {
          "entity": "it",
          "type": "Department",
          "startIndex": 19,
          "endIndex": 20,
          "resolution": {
            "values": [
              "Information Technology"
            ]
          }
        }
      ]
    }
   ```

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informazioni correlate

* Informazioni concettuali sull'[entità elenco](luis-concept-entity-types.md#list-entity)
* [Come eseguire il training](luis-how-to-train.md)
* [Come eseguire la pubblicazione](luis-how-to-publish-app.md)
* [Come testare nel portale LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passaggi successivi
In questa esercitazione è stata creata una nuova finalità, sono state aggiunte espressioni di esempio e quindi è stato creato un elenco di entità per estrarre corrispondenze esatte del testo dalle espressioni. Dopo il training e la pubblicazione dell'app, una query per l'endpoint ha identificato la finalità e restituito i dati estratti.

Continuare con questa app [aggiungendo un'entità composta](luis-tutorial-composite-entity.md).

> [!div class="nextstepaction"]
> [Aggiungere un'entità predefinita con un ruolo all'app](tutorial-entity-roles.md)

