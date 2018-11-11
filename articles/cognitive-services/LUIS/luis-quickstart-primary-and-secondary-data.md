---
title: 'Esercitazione 7: entità semplice con elenco di frasi in LUIS'
titleSuffix: Azure Cognitive Services
description: Estrarre dati di Machine Learning da un'espressione
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: 4647e663f4a2734451835f90d2687bb6aaf91712
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277801"
---
# <a name="tutorial-7-extract-names-with-simple-entity-and-phrase-list"></a>Esercitazione 7: estrarre nomi con entità semplice ed elenco di frasi

Questa esercitazione illustra come estrarre dati di Machine Learning relativi a un nome di mansione da un'espressione usando l'entità **semplice**. Per aumentare l'accuratezza di estrazione, aggiungere un elenco di frasi contenente termini specifici all'entità semplice.

In questa esercitazione verrà aggiunta una nuova entità per estrarre il nome di mansione. Lo scopo dell'entità semplice in questa app LUIS è quello di indicare a LUIS cos'è un nome di mansione e dove trovarlo in un'espressione. La parte dell'espressione che rappresenta il nome della mansione può variare da espressione a espressione, a seconda della scelta delle parole e della lunghezza dell'espressione. LUIS necessita di esempi di nomi di mansione tra tutte le finalità che utilizzano nomi di mansione.  

Un'entità semplice è una scelta appropriata per questo tipo di dati quando:

* I dati rappresentano un singolo concetto.
* I dati non sono formattati in modo corretto, ad esempio un'espressione regolare.
* I dati non sono comuni, ad esempio un'entità predefinita riguardante un numero di telefono o dati.
* I dati non corrispondono esattamente a un elenco di parole conosciute, ad esempio un'entità elenco.
* I dati non contengono altri elementi di dati, ad esempio un'entità composta o gerarchica.

**In questa esercitazione si apprenderà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Usare l'app di esercitazione esistente
> * Aggiungere un'entità semplice per estrarre le mansioni dall'app
> * Aggiungere un elenco di frasi per aumentare il segnale per le parole relative alle mansioni
> * Eseguire il training 
> * Pubblica 
> * Ottenere finalità ed entità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="use-existing-app"></a>Usare l'app esistente

Continuare con l'app creata nell'ultima esercitazione denominata **HumanResources**. 

Se non si dispone dell'app HumanResources dell'esercitazione precedente, usare la procedura seguente:

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorials/custom-domain-composite-HumanResources.json).

2. Importare il file JSON in una nuova app.

3. Nella scheda **Versioni** della sezione **Gestisci**, clonare la versione e denominarla `simple`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL.

## <a name="simple-entity"></a>Entità semplice
L'entità semplice rileva un singolo concetto di dati contenuto in parole o frasi.

Considerare le espressioni seguenti di un chatbot:

|Espressione|Nome della mansione estraibile|
|:--|:--|
|I want to apply for the new accounting job.|accounting|
|Submit my resume for the engineering position.|engineering|
|Fill out application for job 123456|123456|

Il nome della mansione è difficile da determinare perché può essere un sostantivo, un verbo o una frase di diverse parole. Ad esempio:

|Processi|
|--|
|engineer|
|software engineer|
|senior software engineer|
|engineering team lead |
|air traffic controller|
|motor vehicle operator|
|ambulance driver|
|tender|
|extruder|
|millwright|

Questa app LUIS include nomi di mansioni per diverse finalità. Assegnando etichette a queste parole nelle espressioni per tutte le finalità, LUIS apprende cos'è un nome di mansione e dove si trova all'interno delle espressioni.

Dopo che le entità sono contrassegnate nelle espressioni di esempio, è importante aggiungere un elenco di frasi per migliorare il segnale dell'entità semplice. Un elenco di frasi **non** viene usato come una corrispondenza esatta e non dovrà corrispondere a ogni possibile valore previsto. 

1. [!INCLUDE[Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

2. Nella pagina **Intents** (Finalità) selezionare la finalità **ApplyForJob**. 

3. Nell'espressione `I want to apply for the new accounting job` selezionare `accounting` immettere `Job` nel campo in alto nel menu a comparsa e quindi scegliere **Create new entity** (Crea nuova entità) dal menu a comparsa. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Screenshot di LUIS con la finalità \"ApplyForJob\" con i passaggi di creazione dell'entità evidenziati")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. Nella finestra popup verificare il nome dell'entità e selezionare **Done** (Fine).

    ![Finestra di dialogo modale popup di creazione di un'entità semplice con nome Job e tipo Simple](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. Nell'espressione `Submit resume for engineering position` etichettare la parola `engineering` come entità Job. Selezionare la parola `engineering` e quindi scegliere **Job** dal menu a comparsa. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Screenshot di LUIS con l'etichetta dell'entità Job evidenziata ")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)

    Tutte le espressioni sono etichettate, ma cinque espressioni non sono sufficienti per fornire informazioni a LUIS sulle parole e sulle frasi correlate alle mansioni. Per le mansioni che usano un valore numerico non sono necessari altri esempi perché viene usata un'entità espressione regolare. Le mansioni indicate da parole o frasi necessitano di almeno altri 15 esempi. 

6. Aggiungere altre espressioni e contrassegnare le parole o le frasi relative alle mansioni come entità **Job**. I tipi di mansione sono generali per un servizio per l'impiego. Se si vuole disporre di mansioni correlate a un settore specifico, usare parole relative a mansioni di tale settore. 

    |Espressione|Entità Job|
    |:--|:--|
    |I'm applying for the Program Manager desk in R&D|Program Manager|
    |Here is my line cook application.|line cook|
    |My resume for camp counselor is attached.|camp counselor|
    |This is my c.v. for administrative assistant.|administrative assistant|
    |I want to apply for the management job in sales.|management, sales|
    |This is my resume for the new accounting position.|accounting|
    |My application for barback is included.|barback|
    |I'm submitting my application for roofer and framer.|roofer, framer|
    |My c.v. for bus driver is here.|bus driver|
    |I'm a registered nurse. Here is my resume.|registered nurse|
    |I would like to submit my paperwork for the teaching position I saw in the paper.|teaching|
    |This is my c.v. for the stocker post in fruits and vegetables.|stocker|
    |Apply for tile work.|riquadro|
    |Attached resume for landscape architect.|landscape architect|
    |My curriculum vitae for professor of biology is enclosed.|professor of biology|
    |I would like to apply for the position in photography.|photography|git 

## <a name="label-entity-in-example-utterances"></a>Etichettare entità in espressioni di esempio

Etichettando, o _contrassegnando_, l'entità permette di indicare a LUIS dove si trova l'entità nelle espressioni di esempio.

1. Selezionare **Intents** (Finalità) dal menu a sinistra.

2. Selezionare **GetJobInformation** nell'elenco di finalità. 

3. Etichettare le mansioni nelle espressioni di esempio:

    |Espressione|Entità Job|
    |:--|:--|
    |Is there any work in databases?|database|
    |Looking for a new situation with responsibilities in accounting|accounting|
    |What positions are available for senior engineers?|senior engineers|

    Ci sono altre espressioni di esempio, che tuttavia non contengono parole correlate alle mansioni.

## <a name="train"></a>Eseguire il training

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Pubblica

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entities-from-endpoint"></a>Ottenere finalità ed entità dall'endpoint 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Andare alla fine dell'URL nell'indirizzo e immettere `Here is my c.v. for the programmer job`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire le espressioni `ApplyForJob`.

    ```JSON
    {
      "query": "Here is my c.v. for the programmer job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9826467
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9826467
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0218927357
        },
        {
          "intent": "MoveEmployee",
          "score": 0.007849265
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.00349470088
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00348804821
        },
        {
          "intent": "None",
          "score": 0.00319909188
        },
        {
          "intent": "FindForm",
          "score": 0.00222647213
        },
        {
          "intent": "Utilities.Help",
          "score": 0.00211193133
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.00172086991
        },
        {
          "intent": "Utilities.Cancel",
          "score": 0.00138010911
        }
      ],
      "entities": [
        {
          "entity": "programmer",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 33,
          "score": 0.5230502
        }
      ]
    }
    ```
    
    LUIS ha trovato la finalità corretta, **ApplyForJob**, e ha estratto l'entità corretta, estratti **Job**, con un valore di `programmer`.


## <a name="names-are-tricky"></a>Complessità dei nomi
L'app LUIS ha trovato la finalità corretta con un elevato livello di attendibilità e ha estratto il nome della mansione, ma i nomi possono presentare alcune difficoltà. Provare l'espressione `This is the lead welder paperwork`.  

Nel file JSON seguente LUIS risponde con la finalità corretta, `ApplyForJob`, ma non estrae il nome della mansione `lead welder`. 

```JSON
{
  "query": "This is the lead welder paperwork.",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.468558252
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.468558252
    },
    {
      "intent": "GetJobInformation",
      "score": 0.0102701457
    },
    {
      "intent": "MoveEmployee",
      "score": 0.009442534
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00639619166
    },
    {
      "intent": "None",
      "score": 0.005859333
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.005087704
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.00315379258
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00259344373
    },
    {
      "intent": "FindForm",
      "score": 0.00193389168
    },
    {
      "intent": "Utilities.Confirm",
      "score": 0.000420796918
    }
  ],
  "entities": []
}
```

Poiché un nome può essere di vario tipo, LUIS prevede le entità più accuratamente se dispone di un elenco di parole per aumentare il segnale.

## <a name="to-boost-signal-add-phrase-list"></a>Aggiungere un elenco di parole per aumentare il segnale

Aprire il file [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) nel repository GitHub LUIS-Samples. L'elenco contiene oltre mille parole e frasi correlate alle mansioni. Esaminare l'elenco per individuare le parole relative alle mansioni significative per l'uso previsto. Se alcune parole o frasi non sono presenti nell'elenco, aggiungerle.

1. Nella sezione **Build** (Compila) dell'app LUIS scegliere **Phrase lists** (Elenchi frasi) dal menu **Improve app performance** (Migliora prestazioni app).

2. Selezionare **Create new phrase list** (Crea nuovo elenco di frasi). 

3. Assegnare al nuovo elenco di frasi il nome `Job` e copiare l'elenco dal file jobs-phrase-list.csv nella casella di testo **Values** (Valori). Premere INVIO. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Screenshot della finestra popup di creazione di un nuovo elenco di frasi")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Se si vuole aggiungere più parole all'elenco di frasi, esaminare **Related Values** (Valori correlati) e aggiungere quelle pertinenti. 

4. Fare clic su **Save** (Salva) per attivare l'elenco di frasi.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Screenshot della finestra popup di creazione di un nuovo elenco di frasi con le parole nella casella dei valori dell'elenco di frasi")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. [Eseguire il training](#train) dell'app e [pubblicarla](#publish) di nuovo per usare l'elenco di frasi.

6. Eseguire una nuova query sull'endpoint con la stessa espressione: `This is the lead welder paperwork.`

    La risposta JSON include l'entità estratta:

    ```JSON
    {
        "query": "This is the lead welder paperwork.",
        "topScoringIntent": {
            "intent": "ApplyForJob",
            "score": 0.920025647
        },
        "intents": [
            {
            "intent": "ApplyForJob",
            "score": 0.920025647
            },
            {
            "intent": "GetJobInformation",
            "score": 0.003800706
            },
            {
            "intent": "Utilities.StartOver",
            "score": 0.00299335527
            },
            {
            "intent": "MoveEmployee",
            "score": 0.0027167045
            },
            {
            "intent": "None",
            "score": 0.00259556063
            },
            {
            "intent": "FindForm",
            "score": 0.00224019377
            },
            {
            "intent": "Utilities.Stop",
            "score": 0.00200693542
            },
            {
            "intent": "Utilities.Cancel",
            "score": 0.00195913855
            },
            {
            "intent": "Utilities.Help",
            "score": 0.00162656687
            },
            {
            "intent": "Utilities.Confirm",
            "score": 0.0002851904
            }
        ],
        "entities": [
            {
            "entity": "lead welder",
            "type": "Job",
            "startIndex": 12,
            "endIndex": 22,
            "score": 0.8295959
            }
        ]
    }
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, l'app Human Resources usa un'entità semplice di Machine Learning per trovare i nomi di mansioni nelle espressioni. Poiché i nomi delle mansioni possono consistere in un'ampia gamma di parole o frasi, l'app necessita di un elenco di frasi per migliorare le parole che corrispondono a nomi di mansioni. 

> [!div class="nextstepaction"]
> [Aggiungere un'entità KeyPhrase predefinita](luis-quickstart-intent-and-key-phrase.md)