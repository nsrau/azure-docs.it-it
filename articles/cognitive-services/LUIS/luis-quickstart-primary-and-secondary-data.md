---
title: 'Esercitazione: Entità semplice, elenco di frasi - LUIS'
titleSuffix: Azure Cognitive Services
description: Questa esercitazione illustra come estrarre dati di Machine Learning relativi a un nome di processo da un'espressione usando l'entità semplice. Per aumentare l'accuratezza di estrazione, aggiungere un elenco di frasi contenente termini specifici all'entità semplice.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: diberry
ms.openlocfilehash: 87e4fe3671f419383cb342fdb7dca55a8d2eb45d
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376257"
---
# <a name="tutorial-extract-names-with-simple-entity-and-a-phrase-list"></a>Esercitazione: Estrarre nomi con entità semplice ed elenco di frasi

Questa esercitazione illustra come estrarre dati di Machine Learning relativi a un nome di mansione da un'espressione usando l'entità **semplice**. Per aumentare l'accuratezza di estrazione, aggiungere un elenco di frasi contenente termini specifici all'entità semplice.

L'entità semplice rileva un singolo concetto di dati contenuto in parole o frasi.

**In questa esercitazione si imparerà come:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importare l'app di esempio
> * Aggiungere l'entità semplice 
> * Aggiungere un elenco di frasi per aumentare il segnale per le parole
> * Eseguire il training 
> * Pubblica 
> * Ottenere finalità ed entità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]


## <a name="simple-entity"></a>Entità semplice

In questa esercitazione verrà aggiunta una nuova entità per estrarre il nome di mansione. Lo scopo dell'entità semplice in questa app LUIS è quello di indicare a LUIS cos'è un nome di mansione e dove trovarlo in un'espressione. La parte dell'espressione che rappresenta il nome della mansione può variare da espressione a espressione, a seconda della scelta delle parole e della lunghezza dell'espressione. LUIS necessita di esempi di nomi di mansione tra tutte le finalità che utilizzano nomi di mansione.  

Un'entità semplice è una scelta appropriata per questo tipo di dati quando:

* I dati rappresentano un singolo concetto.
* I dati non sono formattati in modo corretto, ad esempio un'espressione regolare.
* I dati non sono comuni, ad esempio un'entità predefinita riguardante un numero di telefono o dati.
* I dati non corrispondono esattamente a un elenco di parole conosciute, ad esempio un'entità elenco.
* I dati non contengono altri elementi di dati, ad esempio un'entità composta o ruoli contestuali.

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

## <a name="import-example-app"></a>Importare l'app di esempio

1.  Scaricare e salvare il [file JSON dell'app](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/build-app/intentonly.json) dall'esercitazione sulle finalità.

2. Importare il file JSON in una nuova app.

3. Nella scheda **Versioni** della sezione **Gestisci**, clonare la versione e denominarla `simple`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. Poiché viene usato come parte della route dell'URL, il nome della versione non può contenere caratteri non validi per un URL.

## <a name="mark-entities-in-example-utterances-of-an-intent"></a>Contrassegnare le entità nelle espressioni di esempio di una finalità

1. [!INCLUDE [Start in Build section](../../../includes/cognitive-services-luis-tutorial-build-section.md)]

1. Nella pagina **Intents** (Finalità) selezionare la finalità **ApplyForJob**. 

1. Nell'espressione `I want to apply for the new accounting job` selezionare `accounting` immettere `Job` nel campo in alto nel menu a comparsa e quindi scegliere **Create new entity** (Crea nuova entità) dal menu a comparsa. 

    [![Screenshot di LUIS con finalità 'ApplyForJob' con evidenziati i passaggi di creazione dell'entità](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Screenshot di LUIS con finalità 'ApplyForJob' con evidenziati i passaggi di creazione dell'entità")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

1. Nella finestra popup verificare il nome dell'entità e selezionare **Done** (Fine).

    ![Finestra di dialogo modale popup di creazione di un'entità semplice con nome Job e tipo Simple](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

1. Nelle espressioni rimanenti, contrassegnare le parole correlate alle mansioni con l'entità **Job** selezionando la parola o frase, quindi scegliere **Job** dal menu a comparsa. 

    [![Screenshot dell'assegnazione di etichette da parte di LUIS a entità processo](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png "Screenshot dell'assegnazione di etichette da parte di LUIS a entità processo")](media/luis-quickstart-primary-and-secondary-data/hr-label-simple-entity.png#lightbox)


## <a name="add-more-example-utterances-and-mark-entity"></a>Aggiungere altre espressioni di esempio e contrassegnare l'entità

Le entità semplici hanno bisogno di molti esempi per avere un'attendibilità elevata delle stime. 
 
1. Aggiungere altre espressioni e contrassegnare le parole o le frasi relative alle mansioni come entità **Job**. 

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
    |I would like to apply for the position in photography.|photography|

## <a name="mark-job-entity-in-other-intents"></a>Contrassegnare l'entità Job in altre finalità

1. Selezionare **Intents** (Finalità) dal menu a sinistra.

1. Selezionare **GetJobInformation** nell'elenco di finalità. 

1. Etichettare le mansioni nelle espressioni di esempio

    Se in una finalità sono presenti più espressioni di esempio rispetto a un'altra, tale finalità ha maggiori probabilità di ottenere il punteggio di previsione più elevato. 

## <a name="train-the-app-so-the-changes-to-the-intent-can-be-tested"></a>Eseguire il training dell'app in modo che sia possibile testare la finalità 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-so-the-trained-model-is-queryable-from-the-endpoint"></a>Pubblicare l'app in modo che sia possibile eseguire query dall'endpoint sul modello con training

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="get-intent-and-entity-prediction-from-endpoint"></a>Ottenere la stima di finalità ed entità dall'endpoint 

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Andare alla fine dell'URL nell'indirizzo e immettere `Here is my c.v. for the engineering job`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire le espressioni `ApplyForJob`.

    ```json
    {
      "query": "Here is my c.v. for the engineering job",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.98052007
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.98052007
        },
        {
          "intent": "GetJobInformation",
          "score": 0.03424581
        },
        {
          "intent": "None",
          "score": 0.0015820954
        }
      ],
      "entities": [
        {
          "entity": "engineering",
          "type": "Job",
          "startIndex": 24,
          "endIndex": 34,
          "score": 0.668959737
        }
      ]
    }
    ```
    
    LUIS ha trovato la finalità corretta, **ApplyForJob**, e ha estratto l'entità corretta, estratti **Job**, con un valore di `engineering`.


## <a name="names-are-tricky"></a>Complessità dei nomi
L'app LUIS ha trovato la finalità corretta con un elevato livello di attendibilità e ha estratto il nome della mansione, ma i nomi possono presentare alcune difficoltà. Provare l'espressione `This is the lead welder paperwork`.  

Nel file JSON seguente LUIS risponde con la finalità corretta, `ApplyForJob`, ma non estrae il nome della mansione `lead welder`. 

```json
{
  "query": "This is the lead welder paperwork",
  "topScoringIntent": {
    "intent": "ApplyForJob",
    "score": 0.860295951
  },
  "intents": [
    {
      "intent": "ApplyForJob",
      "score": 0.860295951
    },
    {
      "intent": "GetJobInformation",
      "score": 0.07265678
    },
    {
      "intent": "None",
      "score": 0.00482481951
    }
  ],
  "entities": []
}
```

Poiché un nome può essere di vario tipo, LUIS prevede le entità più accuratamente se dispone di un elenco di parole per aumentare il segnale.

## <a name="to-boost-signal-of-the-job-related-words-add-a-phrase-list-of-job-related-words"></a>Per aumentare il segnale delle parole correlate alle mansioni, aggiungere un elenco di frasi di parole correlate alle mansioni

Aprire il file [jobs-phrase-list.csv](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) nel repository GitHub degli esempi di Azure. L'elenco contiene oltre 1.000 parole e frasi correlate alle mansioni. Esaminare l'elenco per individuare le parole relative alle mansioni significative per l'uso previsto. Se alcune parole o frasi non sono presenti nell'elenco, aggiungerle.

1. Nella sezione **Build** (Compila) dell'app LUIS scegliere **Phrase lists** (Elenchi frasi) dal menu **Improve app performance** (Migliora prestazioni app).

1. Selezionare **Create new phrase list** (Crea nuovo elenco di frasi). 

1. Assegnare al nuovo elenco di frasi il nome `JobNames` e copiare l'elenco dal file jobs-phrase-list.csv nella casella di testo **Values** (Valori).

    [![Screenshot della finestra popup di creazione di un nuovo elenco di frasi](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Screenshot della finestra popup di creazione di un nuovo elenco di frasi")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Se si vogliono aggiungere altre parole all'elenco di frasi, selezionare **Recommend** (Consiglia), quindi esaminare **Related Values** (Valori correlati) e aggiungere quelle pertinenti. 

    Verificare che l'opzione **These values are interchangeable** (Questi valori sono intercambiabili) sia selezionata perché questi valori devono essere tutti considerati sinonimi delle mansioni. Vedere altre informazioni sui [concetti relativi agli elenchi di frasi](luis-concept-feature.md#how-to-use-phrase-lists) intercambiabili e non intercambiabili.

1. Selezionare **Done** (Fine) per attivare l'elenco di frasi.

    [![Screenshot della finestra popup di creazione di un nuovo elenco di frasi con parole nella casella dei valori dell'elenco di frasi](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Screenshot della finestra popup di creazione di un nuovo elenco di frasi con parole nella casella dei valori dell'elenco di frasi")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

1. Eseguire il training dell'app e pubblicarla di nuovo per usare l'elenco di frasi.

1. Eseguire una nuova query sull'endpoint con la stessa espressione: `This is the lead welder paperwork.`

    La risposta JSON include l'entità estratta:

    ```json
      {
      "query": "This is the lead welder paperwork.",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.983076453
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.983076453
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0120766377
        },
        {
          "intent": "None",
          "score": 0.00248388131
        }
      ],
      "entities": [
        {
          "entity": "lead welder",
          "type": "Job",
          "startIndex": 12,
          "endIndex": 22,
          "score": 0.8373154
        }
      ]
    }
    ```

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informazioni correlate

* [Esercitazione sulle finalità senza entità](luis-quickstart-intents-only.md)
* Informazioni concettuali sull'[entità semplice](luis-concept-entity-types.md)
* Informazioni concettuali sull'[elenco di frasi](luis-concept-feature.md)
* [Come eseguire il training](luis-how-to-train.md)
* [Come eseguire la pubblicazione](luis-how-to-publish-app.md)
* [Come testare nel portale LUIS](luis-interactive-test.md)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione, l'app Human Resources usa un'entità semplice di Machine Learning per trovare i nomi di mansioni nelle espressioni. Poiché i nomi delle mansioni possono consistere in un'ampia gamma di parole o frasi, l'app necessita di un elenco di frasi per migliorare le parole che corrispondono a nomi di mansioni. 

> [!div class="nextstepaction"]
> [Aggiungere un'entità KeyPhrase predefinita](luis-quickstart-intent-and-key-phrase.md)
