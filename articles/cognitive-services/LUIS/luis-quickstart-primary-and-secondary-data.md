---
title: "Esercitazione: Creazione di un'app di Language Understanding per estrarre dati - Azure | Microsoft Docs"
description: Questa esercitazione illustra come creare una semplice app di Language Understanding usando finalità e un'entità semplice per estrarre dati di Machine Learning.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/26/2018
ms.author: v-geberr
ms.openlocfilehash: b718ed505babd2df6487aecd3a87f17590aef2b9
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061248"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>Esercitazione: Creare un'app che usa un'entità semplice
In questa esercitazione si crea un'app che illustra come estrarre dati di Machine Learning da un'espressione usando l'entità **semplice**.

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendere le entità semplici 
> * Creare una nuova app LUIS per il dominio delle risorse umane 
> * Aggiungere un'entità semplice per estrarre le mansioni dall'app
> * Eseguire il training e pubblicare l'app
> * Eseguire query sull'endpoint dell'app per ottenere una risposta JSON di Language Understanding
> * Aggiungere un elenco di frasi per aumentare il segnale per le parole relative alle mansioni
> * Eseguire il training dell'app, pubblicarla ed eseguire di nuovo la query sull'endpoint

Per questo articolo è necessario un account [LUIS](luis-reference-regions.md#luis-website) gratuito per creare un'applicazione LUIS personalizzata.

## <a name="before-you-begin"></a>Prima di iniziare
Se non si ha l'app relativa alle risorse umane dell'esercitazione sull'[entità gerarchica](luis-quickstart-intent-and-hier-entity.md), [importare](create-new-app.md#import-new-app) il codice JSON in una nuova app nel sito Web [LUIS](luis-reference-regions.md#luis-website). L'app da importare è disponibile nel repository GitHub [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-hier-HumanResources.json).

Se si vuole mantenere l'app Risorse umane originale, clonare la versione nella pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) (Impostazioni) assegnando il nome `simple`. La clonazione è un ottimo modo per provare le diverse funzionalità di Language Understanding senza modificare la versione originale.  

## <a name="purpose-of-the-app"></a>Scopo dell'app
Questa app illustra come eseguire il pull dei dati da un'espressione. Considerare le espressioni seguenti di un chatbot:

|Espressione|Nome della mansione estraibile|
|:--|:--|
|I want to apply for the new accounting job.|accounting|
|Please submit my resume for the engineering position.|engineering|
|Fill out application for job 123456|123456|

Questa esercitazione aggiunge una nuova entità per estrarre il nome della mansione. La possibilità di estrarre un numero di mansione specifico è illustrata nell'[esercitazione](luis-quickstart-intents-regex-entity.md) relativa alle espressioni regolari. 

## <a name="purpose-of-the-simple-entity"></a>Scopo dell'entità semplice
Lo scopo dell'entità semplice in questa app LUIS è quello di indicare a LUIS cos'è un nome di mansione e dove trovarlo in un'espressione. La parte dell'espressione che rappresenta la mansione può variare da espressione a espressione, a seconda della scelta delle parole e della lunghezza dell'espressione. LUIS necessita di esempi di mansioni nelle espressioni per tutte le finalità.  

Il nome della mansione è difficile da determinare perché può essere un sostantivo, un verbo o una frase di diverse parole. Ad esempio: 

|Mansioni|
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

Questa app LUIS include nomi di mansioni per diverse finalità. Assegnando etichette a queste parole nelle espressioni per tutte le finalità, LUIS apprende cos'è una mansione e dove si trova all'interno delle espressioni.

## <a name="create-job-simple-entity"></a>Creare un'entità mansione semplice

1. Assicurarsi che l'app Risorse umane sia presente nella sezione **Build** di Language Understanding. È possibile passare a questa sezione selezionando **Build** nella barra dei menu in alto a destra. 

    [ ![Screenshot dell'app LUIS con il pulsante relativo alla compilazione evidenziato in alto a destra sulla barra di spostamento](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png)](./media/luis-quickstart-primary-and-secondary-data/hr-first-image.png#lightbox)

2. Nella pagina **Intents** (Finalità) selezionare la finalità **ApplyForJob**. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png "Screenshot di LUIS con la finalità \"ApplyForJob\" evidenziata")](media/luis-quickstart-primary-and-secondary-data/hr-select-applyforjob.png#lightbox)

3. Nell'espressione `I want to apply for the new accounting job` selezionare `accounting` immettere `Job` nel campo in alto nel menu a comparsa e quindi scegliere **Create new entity** (Crea nuova entità) dal menu a comparsa. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png "Screenshot di LUIS con la finalità \"ApplyForJob\" con i passaggi di creazione dell'entità evidenziati")](media/luis-quickstart-primary-and-secondary-data/hr-create-entity.png#lightbox)

4. Nella finestra popup verificare il nome dell'entità e selezionare **Done** (Fine).

    ![Finestra di dialogo modale popup di creazione di un'entità semplice con nome Job e tipo Simple](media/luis-quickstart-primary-and-secondary-data/hr-create-simple-entity-popup.png)

5. Nell'espressione `Submit resume for engineering position` etichettare la parola engineering come entità Job. Selezionare la parola engineering e quindi scegliere Job dal menu a comparsa. 

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

## <a name="label-entity-in-example-utterances-for-getjobinformation-intent"></a>Etichettare le entità nelle espressioni di esempio per la finalità GetJobInformation
1. Selezionare **Intents** (Finalità) dal menu a sinistra.

2. Selezionare **GetJobInformation** nell'elenco di finalità. 

3. Etichettare le mansioni nelle espressioni di esempio:

    |Espressione|Entità Job|
    |:--|:--|
    |Is there any work in databases?|database|
    |Looking for a new situation with responsibilities in accounting|accounting|
    |What positions are available for senior engineers?|senior engineers|

    Ci sono altre espressioni di esempio, che tuttavia non contengono parole correlate alle mansioni.

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding
Language Understanding non rileva le modifiche a finalità ed entità (il modello) finché non viene eseguito il training. 

1. Nella parte superiore destra del sito Web di Language Understanding, selezionare il pulsante **Train** (Esegui il training).

    ![Selezionare il pulsante Train (Esegui il training)](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. Il training è completato quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

    ![Notifica di training completato](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint
Per ottenere una previsione di Language Understanding in un chatbot o in un'altra applicazione, è necessario pubblicare l'app. 

1. Nella parte superiore destra del sito Web di Language Understanding, selezionare il pulsante **Publish** (Pubblica). 

2. Selezionare lo slot di produzione, quindi fare clic sul pulsante **Publish** (Pubblica).

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "Screenshot della pagina Publish (Pubblica) con il pulsante Publish to production slot (Pubblica in slot di produzione) evidenziato")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. La pubblicazione è completata quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Eseguire una query sull'endpoint con un'espressione diversa
Nella pagina **Publish** (Pubblica) selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "Schermata della pagina Publish (Pubblica) con l'endpoint evidenziato")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

Questa azione apre un'altra finestra del browser con l'URL endpoint nella barra degli indirizzi. Passare alla fine dell'URL nell'indirizzo e immettere `Here is my c.v. for the programmer job`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire le espressioni `ApplyForJob`.

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

## <a name="to-boost-signal-add-jobs-phrase-list"></a>Aggiungere un elenco di parole relative alle mansioni per aumentare il segnale
Aprire il file [jobs-phrase-list.csv](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/job-phrase-list.csv) nel repository GitHub LUIS-Samples. L'elenco contiene oltre mille parole e frasi correlate alle mansioni. Esaminare l'elenco per individuare le parole relative alle mansioni significative per l'uso previsto. Se alcune parole o frasi non sono presenti nell'elenco, aggiungerle.

1. Nella sezione **Build** (Compila) dell'app LUIS scegliere **Phrase lists** (Elenchi frasi) dal menu **Improve app performance** (Migliora prestazioni app).

    [![](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png "Screenshot con l'opzione relativa agli elenchi di frasi evidenziata nel riquadro di spostamento a sinistra")](media/luis-quickstart-primary-and-secondary-data/hr-select-phrase-list-left-nav.png#lightbox)

2. Selezionare **Create new phrase list** (Crea nuovo elenco di frasi). 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png "Screenshot con il pulsante per la creazione di un nuovo elenco di frasi evidenziato")](media/luis-quickstart-primary-and-secondary-data/hr-create-new-phrase-list.png#lightbox)

3. Assegnare al nuovo elenco di frasi il nome `Jobs` e copiare l'elenco dal file jobs-phrase-list.csv nella casella di testo **Values** (Valori). Premere INVIO. 

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png "Screenshot della finestra popup di creazione di un nuovo elenco di frasi")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-1.png#lightbox)

    Se si vuole aggiungere più parole all'elenco di frasi, esaminare le parole consigliate e aggiungere quelle pertinenti. 

4. Fare clic su **Save** (Salva) per attivare l'elenco di frasi.

    [![](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png "Screenshot della finestra popup di creazione di un nuovo elenco di frasi con le parole nella casella dei valori dell'elenco di frasi")](media/luis-quickstart-primary-and-secondary-data/hr-create-phrase-list-2.png#lightbox)

5. [Eseguire il training](#train-the-luis-app) dell'app e [pubblicarla](#publish-the-app-to-get-the-endpoint-URL) di nuovo per usare l'elenco di frasi.

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

## <a name="phrase-lists"></a>Elenchi di frasi
L'aggiunta dell'elenco di frasi ha aumentato il segnale delle parole nell'elenco ma **non** è stata usata una corrispondenza esatta. L'elenco di frasi include diverse mansioni con la prima parola corrispondente a `lead` e include anche la mansione `welder`, ma non `lead welder`. Questo elenco di frasi per le mansioni potrebbe non essere completo. Man mano che si [rivedono le espressioni dell'endpoint](label-suggested-utterances.md) su base regolare e si trovano altre parole relative alle mansioni, aggiungerle all'elenco di frasi. Quindi ripetere il training e la pubblicazione.

## <a name="what-has-this-luis-app-accomplished"></a>Quali attività ha eseguito l'app di Language Understanding?
Con un'entità semplice e un elenco di parole, quest'app ha identificato una finalità di query in linguaggio naturale e ha restituito i dati del messaggio. 

Il chatbot ha ora informazioni sufficienti per determinare l'azione principale di candidatura per una mansione e un parametro di tale azione, che fa riferimento alla mansione. 

## <a name="where-is-this-luis-data-used"></a>Qual è la destinazione d'uso dei dati di Language Understanding? 
Language Understanding ha completato le attività relative alla richiesta. L'applicazione chiamante, ad esempio un chatbot, può prendere il risultato topScoringIntent e i dati dell'entità per usare un'API di terze parti per inviare le informazioni sulla mansione a un rappresentante del reparto risorse umane. Se sono presenti altre opzioni a livello di codice per l'applicazione chiamante o il bot, Language Understanding non esegue tali operazioni, ma determina solo la finalità dell'utente. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app di Language Understanding. A tale scopo, selezionare il menu con i puntini di sospensione (...) a destra del nome dell'app nell'elenco di app, quindi selezionare **Delete** (Elimina). Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come aggiungere un'entità KeyPhrase predefinita](luis-quickstart-intent-and-key-phrase.md)