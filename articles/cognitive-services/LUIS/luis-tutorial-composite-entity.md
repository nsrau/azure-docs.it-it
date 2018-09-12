---
title: Esercitazione per la creazione di un'entità composita per estrarre dati complessi - Azure | Microsoft Docs
description: Informazioni su come creare un'entità composita nell'app LUIS per estrarre tipi diversi di dati di entità.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: 17a8110624975d8053ad69c5bf30477e6d715ee8
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/07/2018
ms.locfileid: "44159827"
---
# <a name="tutorial-6-add-composite-entity"></a>Esercitazione: 6. Aggiungere un'entità composita 
Questa esercitazione aggiunge un'entità composita per aggregare i dati estratti in un'entità contenitore.

In questa esercitazione si apprenderà come:

<!-- green checkmark -->
> [!div class="checklist"]
> * Informazioni sulle entità composite 
> * Aggiungere un'entità composita per estrarre i dati
> * Eseguire il training e pubblicare l'app
> * Eseguire query sull'endpoint dell'app per ottenere una risposta JSON di Language Understanding

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>Prima di iniziare
Se non si ha l'app relativa alle risorse umane dell'esercitazione sull'[entità gerarchica](luis-quickstart-intent-and-hier-entity.md), [importare](luis-how-to-start-new-app.md#import-new-app) il codice JSON in una nuova app nel sito Web [LUIS](luis-reference-regions.md#luis-website). L'app da importare è disponibile nel repository GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json).

Se si vuole mantenere l'app relativa alle risorse umane originale, clonare la versione nella pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) (Impostazioni) assegnando il nome `composite`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale.  

## <a name="composite-entity-is-a-logical-grouping"></a>L'entità composita è un raggruppamento logico 
Lo scopo dell'entità composita è di raggruppare le entità correlate in un'entità di categoria padre. Prima che venga creata un'entità composita le informazioni sono entità separate. È simile all'entità gerarchica ma può contenere più tipi di entità. 

 Creare un'entità composita quando le entità separate possono essere raggruppate logicamente e questo raggruppamento logico è utile per le applicazioni client. 

In questa app, il nome del dipendente è definito nell'entità elenco **Employee** (Dipendente) e include i sinonimi di nome, l'indirizzo e-mail, l'interno telefonico aziendale, il numero di telefono cellulare e Stati Uniti. Il codice fiscale. 

La finalità **MoveEmployee** contiene espressioni di esempio per richiedere il trasferimento di un dipendente da un edificio e ufficio a un altro. I nomi degli edifici sono alfabetici: "A", "B", e così via, mentre gli uffici sono numerici: "1234", "13245". 

Espressioni di esempio nella finalità **MoveEmployee** includono:

|Espressioni di esempio|
|--|
|Move John W. Smith to a-2345|
|shift x12345 to h-1234 tomorrow|
 
La richiesta di trasferimento deve includere almeno il dipendente (con qualsiasi sinonimo) e la posizione finale dell'edificio e dell'ufficio. La richiesta può includere anche l'ufficio di origine oltre a una data in cui eseguire il trasferimento. 

I dati estratti dall'endpoint dovrebbero contenere queste informazioni e restituirle in un'entità composita `RequestEmployeeMove`. 

## <a name="create-composite-entity"></a>Creare un'entità composita
1. Assicurarsi che l'app relativa alle risorse umane sia presente nella sezione **Build** (Compila) di LUIS. È possibile passare a questa sezione selezionando **Build** (Compila) nella barra dei menu in alto a destra. 

2. Nella pagina **Intents** (Finalità) selezionare la finalità **MoveEmployee**. 

3. Selezionare l'icona della lente di ingrandimento della barra degli strumenti per filtrare l'elenco di espressioni. 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "Screenshot di LUIS con la finalità \"MoveEmployee\" con il pulsante della lente di ingrandimento evidenziato")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Immettere `tomorrow` nella casella di testo del filtro per trovare l'espressione `shift x12345 to h-1234 tomorrow`.

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "Screenshot di LUIS con la finalità \"MoveEmployee\" con il filtro \"tomorrow\" evidenziato")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    È anche possibile filtrare l'entità per datetimeV2, selezionando **Entity filters** (Filtri entità) e quindi **datetimeV2** dall'elenco. 

5. Selezionare la prima entità, `Employee`, quindi selezionare **Wrap in composite entity** (Esegui il wrapping in entità composita) nell'elenco del menu a comparsa. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "Screenshot di LUIS con la finalità \"MoveEmployee\" con evidenziata la selezione della prima entità nel composito")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Quindi selezionare immediatamente l'ultima entità, `datetimeV2`, nell'espressione. Sotto le parole selezionate viene disegnata una barra verde che indica un'entità composita. Nel menu a comparsa immettere il nome composito `RequestEmployeeMove` quindi selezionare **Create new composite** (Crea nuovo composito) dal menu a comparsa. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "Screenshot di LUIS con la finalità \"MoveEmployee\" con evidenziate la selezione dell'ultima entità e la creazione dell'entità")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. In **What type of entity do you want to create** (Che tipo di identità si desidera creare?) quasi tutti i campi necessari sono nell'elenco. Manca solo la posizione di origine. Selezionare **Add a child entity** (Aggiungi entità figlio), selezionare **Locations::Origin** dall'elenco delle entità esistenti, quindi selezionare **Done** (Operazione completata). 

    ![Screenshot di LUIS con la finalità "MoveEmployee" in cui si aggiunge un'altra entità nella finestra popup](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Selezionare la lente di ingrandimento nella barra degli strumenti per rimuovere il filtro. 

## <a name="label-example-utterances-with-composite-entity"></a>Etichettare le espressioni di esempio con l'entità composita
1. In ogni espressione di esempio, selezionare l'entità più a sinistra nel composito. Selezionare quindi **Wrap in composite entity** (Esegui il wrapping in entità composita).

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "Screenshot di LUIS con la finalità \"MoveEmployee\" con evidenziata la selezione della prima entità nel composito")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Selezionare l'ultima parola nell'entità composita quindi selezionare **RequestEmployeeMove** nel menu a comparsa. 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "Screenshot di LUIS con la finalità \"MoveEmployee\" con evidenziata la selezione dell'ultima entità nel composito")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Verificare che tutte le espressioni nella finalità siano etichettate con l'entità composita. 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "Schermata di LUIS in 'MoveEmployee' con tutte le espressioni etichettate")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint"></a>Query dell'endpoint 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Andare alla fine dell'URL nell'indirizzo e immettere `Move Jill Jones from a-1234 to z-2345 on March 3 2 p.m.`. L'ultimo parametro querystring è `q`, la query dell'espressione. 

    Poiché questo test consiste nel verificare che il composito venga estratto in modo corretto, il test può includere un'espressione di esempio esistente o una nuova espressione. Un buon test consiste nell'includere tutte le entità figlio nell'entità composita.

    ```JSON
    {
      "query": "Move Jill Jones from a-1234 to z-2345 on March 3  2 p.m",
      "topScoringIntent": {
        "intent": "MoveEmployee",
        "score": 0.9959525
      },
      "intents": [
        {
          "intent": "MoveEmployee",
          "score": 0.9959525
        },
        {
          "intent": "GetJobInformation",
          "score": 0.009858314
        },
        {
          "intent": "ApplyForJob",
          "score": 0.00728598563
        },
        {
          "intent": "FindForm",
          "score": 0.0058053555
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.005371796
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00266987388
        },
        {
          "intent": "None",
          "score": 0.00123299169
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00116407464
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00102653319
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.0006628214
        }
      ],
      "entities": [
        {
          "entity": "march 3 2 p.m",
          "type": "builtin.datetimeV2.datetime",
          "startIndex": 41,
          "endIndex": 54,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2018-03-03 14:00:00"
              },
              {
                "timex": "XXXX-03-03T14",
                "type": "datetime",
                "value": "2019-03-03 14:00:00"
              }
            ]
          }
        },
        {
          "entity": "jill jones",
          "type": "Employee",
          "startIndex": 5,
          "endIndex": 14,
          "resolution": {
            "values": [
              "Employee-45612"
            ]
          }
        },
        {
          "entity": "z - 2345",
          "type": "Locations::Destination",
          "startIndex": 31,
          "endIndex": 36,
          "score": 0.9690751
        },
        {
          "entity": "a - 1234",
          "type": "Locations::Origin",
          "startIndex": 21,
          "endIndex": 26,
          "score": 0.9713137
        },
        {
          "entity": "-1234",
          "type": "builtin.number",
          "startIndex": 22,
          "endIndex": 26,
          "resolution": {
            "value": "-1234"
          }
        },
        {
          "entity": "-2345",
          "type": "builtin.number",
          "startIndex": 32,
          "endIndex": 36,
          "resolution": {
            "value": "-2345"
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 47,
          "endIndex": 47,
          "resolution": {
            "value": "3"
          }
        },
        {
          "entity": "2",
          "type": "builtin.number",
          "startIndex": 50,
          "endIndex": 50,
          "resolution": {
            "value": "2"
          }
        },
        {
          "entity": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "type": "requestemployeemove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "requestemployeemove",
          "value": "jill jones from a - 1234 to z - 2345 on march 3 2 p . m",
          "children": [
            {
              "type": "builtin.datetimeV2.datetime",
              "value": "march 3 2 p.m"
            },
            {
              "type": "Locations::Destination",
              "value": "z - 2345"
            },
            {
              "type": "Employee",
              "value": "jill jones"
            },
            {
              "type": "Locations::Origin",
              "value": "a - 1234"
            }
          ]
        }
      ],
      "sentimentAnalysis": {
        "label": "neutral",
        "score": 0.5
      }
    }
    ```

  Questa espressione restituisce una matrice di entità composite. A ogni entità viene assegnato un tipo e un valore. Per individuare con maggiore precisione ogni entità figlio, usare la combinazione di tipo e valore dell'elemento di matrice composita per trovare l'elemento corrispondente nella matrice delle entità.  

## <a name="what-has-this-luis-app-accomplished"></a>Quali attività ha eseguito l'app di Language Understanding?
Questa app ha identificato una finalità di query in linguaggio naturale e ha restituito i dati estratti come gruppo denominato. 

Il chatbot ha ora informazioni sufficienti per determinare l'azione principale e i relativi dettagli nell'espressione. 

## <a name="where-is-this-luis-data-used"></a>Qual è la destinazione d'uso dei dati di Language Understanding? 
LUIS ha terminato con questa richiesta. L'applicazione chiamante, ad esempio un chatbot, può acquisire il risultato topScoringIntent e i dati dell'entità per completare il passaggio successivo. LUIS non esegue questo lavoro programmatico per il bot o l'applicazione chiamante, ma si limita a determinare l'intenzione dell'utente. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi
> [!div class="nextstepaction"] 
> [Informazioni su come aggiungere un'entità semplice con un elenco di frasi](luis-quickstart-primary-and-secondary-data.md)  