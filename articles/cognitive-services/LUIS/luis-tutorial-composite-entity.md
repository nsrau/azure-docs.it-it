---
title: 'Esercitazione 6: estrazione di dati compositi con entità composta LUIS'
titleSuffix: Azure Cognitive Services
description: Aggiungere un'entità composita per aggregare i dati estratti di vario tipo in una singola entità contenitore. Aggregando i dati, l'applicazione client può estrarre facilmente i dati correlati in diversi tipi di dati.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 99e0b22b663f6edab9646111b390186a6f89a90f
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47035182"
---
# <a name="tutorial-6-group-and-extract-related-data"></a>Esercitazione 6: raggruppare ed estrarre dati correlati
Questa esercitazione illusta come aggiungere un'entità composita per aggregare i dati estratti di vario tipo in una singola entità contenitore. Aggregando i dati, l'applicazione client può estrarre facilmente i dati correlati in diversi tipi di dati.

Lo scopo dell'entità composita è di raggruppare le entità correlate in un'entità di categoria padre. Prima che venga creata un'entità composita le informazioni sono entità separate. È simile all'entità gerarchica ma può contenere diversi tipi di entità. 

L'entità composta è una buona soluzione per questo tipo di dati, perché i dati:

* Sono correlati tra loro. 
* Usano vari tipi di entità.
* Devono essere raggruppati ed elaborati dall'applicazione client come un'unità di informazioni.

**In questa esercitazione si apprenderà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usare l'app di esercitazione esistente
> * Aggiungere un'entità composita 
> * Eseguire il training
> * Pubblica
> * Ottenere finalità ed entità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usare l'app esistente
Continuare con l'app creata nell'ultima esercitazione, denominata **HumanResources**. 

Se non si dispone dell'app HumanResources dell'esercitazione precedente, usare la procedura seguente:

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-hier-HumanResources.json).

2. Importare il file JSON in una nuova app.

3. Dalla sezione **Gestisci**, nella scheda **Versioni**, clonare la versione e denominarla `composite`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché il nome della versione viene usato come parte della route dell'URL, il nome non può contenere caratteri non validi per un URL.


## <a name="composite-entity"></a>Entità composita
Creare un'entità composita quando le entità separate possono essere raggruppate logicamente e questo raggruppamento logico è utile per le applicazioni client. 

In questa app, il nome del dipendente è definito nell'entità elenco **Employee** (Dipendente) e include i sinonimi di nome, l'indirizzo e-mail, l'interno telefonico aziendale, il numero di telefono cellulare e Stati Uniti. Il codice fiscale. 

La finalità **MoveEmployee** contiene espressioni di esempio per richiedere il trasferimento di un dipendente da un edificio e ufficio a un altro. I nomi degli edifici sono alfabetici: "A", "B", e così via, mentre gli uffici sono numerici: "1234", "13245". 

Espressioni di esempio nella finalità **MoveEmployee** includono:

|Espressioni di esempio|
|--|
|Move John W. Smith to a-2345|
|shift x12345 to h-1234 tomorrow|
 
La richiesta di trasferimento deve includere almeno il dipendente (con qualsiasi sinonimo) e la posizione finale dell'edificio e dell'ufficio. La richiesta può includere anche l'ufficio di origine oltre a una data in cui eseguire il trasferimento. 

I dati estratti dall'endpoint devono contenere tali informazioni e restituirle nell'entità composita `RequestEmployeeMove`:

```JSON
"compositeEntities": [
  {
    "parentType": "RequestEmployeeMove",
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
]
```

1. [!include[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Nella pagina **Intents** (Finalità) selezionare la finalità **MoveEmployee**. 

3. Selezionare l'icona della lente di ingrandimento della barra degli strumenti per filtrare l'elenco di espressioni. 

    [![](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png "Screenshot di LUIS con la finalità \"MoveEmployee\" con il pulsante della lente di ingrandimento evidenziato")](media/luis-tutorial-composite-entity/hr-moveemployee-magglass.png#lightbox)

4. Immettere `tomorrow` nella casella di testo del filtro per trovare l'espressione `shift x12345 to h-1234 tomorrow`.

    [![](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png "Screenshot di LUIS con la finalità \"MoveEmployee\" con il filtro \"tomorrow\" evidenziato")](media/luis-tutorial-composite-entity/hr-filter-by-tomorrow.png#lightbox)

    È anche possibile filtrare l'entità per datetimeV2, selezionando **Entity filters** (Filtri entità) e quindi **datetimeV2** dall'elenco. 

5. Selezionare la prima entità, `Employee`, quindi selezionare **Wrap in composite entity** (Esegui il wrapping in entità composita) nell'elenco del menu a comparsa. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-1.png "Screenshot di LUIS con la finalità \"MoveEmployee\" con evidenziata la selezione della prima entità nel composito")](media/luis-tutorial-composite-entity/hr-create-entity-1.png#lightbox)


6. Quindi selezionare immediatamente l'ultima entità, `datetimeV2`, nell'espressione. Sotto le parole selezionate viene disegnata una barra verde che indica un'entità composita. Nel menu a comparsa, immettere il nome composto `RequestEmployeeMove` quindi premere INVIO. 

    [![](media/luis-tutorial-composite-entity/hr-create-entity-2.png "Screenshot di LUIS con la finalità \"MoveEmployee\" con evidenziate la selezione dell'ultima entità e la creazione dell'entità")](media/luis-tutorial-composite-entity/hr-create-entity-2.png#lightbox)

7. In **What type of entity do you want to create** (Che tipo di identità si desidera creare?) quasi tutti i campi necessari sono nell'elenco. Manca solo la posizione di origine. Selezionare **Add a child entity** (Aggiungi entità figlio), selezionare **Locations::Origin** dall'elenco delle entità esistenti, quindi selezionare **Done** (Operazione completata). 

    Si noti che l'entità e il numero predefiniti sono stati aggiunti all'entità composta. Se si può disporre di un'entità predefinita che viene visualizzata tra il token di apertura e il token di chiusura di un'entità composta, l'entità composta deve contenere tali entità predefinite. Se le entità predefinite non sono incluse, l'entità composta non sarà stimata correttamente e la stima sarà eseguita per ogni singolo elemento.

    ![Screenshot di LUIS con la finalità "MoveEmployee" in cui si aggiunge un'altra entità nella finestra popup](media/luis-tutorial-composite-entity/hr-create-entity-ddl.png)

8. Selezionare la lente di ingrandimento nella barra degli strumenti per rimuovere il filtro. 

9. Rimuovere la parola `tomorrow` dal filtro in modo da visualizzare nuovamente tutte le espressioni di esempio. 

## <a name="label-example-utterances-with-composite-entity"></a>Etichettare le espressioni di esempio con l'entità composita


1. In ogni espressione di esempio, selezionare l'entità più a sinistra nel composito. Selezionare quindi **Wrap in composite entity** (Esegui il wrapping in entità composita).

    [![](media/luis-tutorial-composite-entity/hr-label-entity-1.png "Screenshot di LUIS con la finalità \"MoveEmployee\" con evidenziata la selezione della prima entità nel composito")](media/luis-tutorial-composite-entity/hr-label-entity-1.png#lightbox)

2. Selezionare l'ultima parola nell'entità composita quindi selezionare **RequestEmployeeMove** nel menu a comparsa. 

    [![](media/luis-tutorial-composite-entity/hr-label-entity-2.png "Screenshot di LUIS con la finalità \"MoveEmployee\" con evidenziata la selezione dell'ultima entità nel composito")](media/luis-tutorial-composite-entity/hr-label-entity-2.png#lightbox)

3. Verificare che tutte le espressioni nella finalità siano etichettate con l'entità composita. 

    [![](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png "Schermata di LUIS in 'MoveEmployee' con tutte le espressioni etichettate")](media/luis-tutorial-composite-entity/hr-all-utterances-labeled.png#lightbox)

## <a name="train"></a>Eseguire il training

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Pubblica

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Ottenere finalità ed entità dall'endpoint 

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
          "type": "RequestEmployeeMove",
          "startIndex": 5,
          "endIndex": 54,
          "score": 0.4027723
        }
      ],
      "compositeEntities": [
        {
          "parentType": "RequestEmployeeMove",
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
      ]
    }
    ```

  Questa espressione restituisce una matrice di entità composite. A ogni entità viene assegnato un tipo e un valore. Per individuare con maggiore precisione ogni entità figlio, usare la combinazione di tipo e valore dell'elemento di matrice composita per trovare l'elemento corrispondente nella matrice delle entità.  

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stata creata un'entità composta per incapsulare le entità esistenti. In questo modo l'applicazione client trova un gruppo di dati correlati in diversi tipi di dati per continuare la conversazione. Un'applicazione client per questa app Human Resources potrebbe richiedere di specificare il giorno e l'ora in cui lo spostamento deve iniziare e terminare. Potrebbe richiedere anche altre informazioni sullo spostamento, come un numero di telefono fisico. 

> [!div class="nextstepaction"] 
> [Informazioni su come aggiungere un'entità semplice con un elenco di frasi](luis-quickstart-primary-and-secondary-data.md)  