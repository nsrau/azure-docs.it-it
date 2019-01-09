---
title: Prevedere finalità
titleSuffix: Azure Cognitive Services
description: In questa esercitazione si crea un'app personalizzata che stima l'intenzione dell'utente. Questa app è il tipo più semplice di app LUIS perché non estrae i vari elementi di dati dal testo dell'espressione, come gli indirizzi di posta elettronica o le date.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 20cd3931488f3d3cf4728b3022316b685da3277a
ms.sourcegitcommit: 7862449050a220133e5316f0030a259b1c6e3004
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/22/2018
ms.locfileid: "53754261"
---
# <a name="tutorial-build-luis-app-to-determine-user-intentions"></a>Esercitazione: Creare un'app LUIS per determinare le intenzioni dell'utente

In questa esercitazione verrà creata un'app Human Resources (HR) personalizzata che consente di prevedere l'intenzione dell'utente in base all'espressione (testo). 

**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Creare una nuova app 
> * Creare finalità
> * Aggiungere espressioni di esempio
> * Eseguire il training dell'app
> * Pubblicare l'app
> * Ottenere finalità dall'endpoint


[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="user-intentions-as-intents"></a>Intenzioni dell'utente come finalità

Lo scopo dell'app è di determinare l'intenzione della conversazione di un testo discorsivo e con linguaggio naturale: 

`Are there any new positions in the Seattle office?`

Queste intenzioni sono suddivise in **Finalità**. 

Questa applicazione ha poche finalità. 

|Finalità|Scopo|
|--|--|
|ApplyForJob|Determinare se l'utente si sta candidando per un lavoro.|
|GetJobInformation|Determinare se l'utente sta cercando informazioni sul lavoro in generale o su un lavoro specifico.|
|Nessuna|Determinare se l'utente sta chiedendo a cui l'app non è in grado di rispondere. Questa finalità viene fornita come parte della creazione dell'app e non può essere eliminata. |

## <a name="create-a-new-app"></a>Creare una nuova app

[!INCLUDE [Follow these steps to create a new LUIS app](../../../includes/cognitive-services-luis-create-new-app-steps.md)]

## <a name="create-intent-for-job-information"></a>Creare una finalità per informazioni sul lavoro

1. Selezionare **Create new intent** (Crea nuova finalità). Immettere il nome della nuova finalità `GetJobInformation`. Questa finalità è stimata quando un utente vuole informazioni sulle posizioni aperte all'interno dell'azienda. 

    ![Screenshot della finestra di dialogo Language Understanding (LUIS) New intent](media/luis-quickstart-intents-only/create-intent.png "Screenshot della finestra di dialogo Language Understanding (LUIS) New intent")

1. Selezionare **Operazione completata**.

2. Aggiungere a questa finalità diverse espressioni di esempio che verranno presumibilmente usate dagli utenti:

    | Espressioni di esempio|
    |--|
    |Any new jobs posted today?|
    |Are there any new positions in the Seattle office?|
    |Are there any remote worker or telecommute jobs open for engineers?|
    |Is there any work in databases?|
    |I'm looking for a co-working situation in the tampa office.|
    |Is there an internship in the san francisco office?|
    |Is there any part-time work for people in college?|
    |Looking for a new situation with responsibilities in accounting|
    |Looking for a job in new york city for bilingual speakers.|
    |Looking for a new situation with responsibilities in accounting.|
    |New jobs?|
    |Show me all the jobs for engineers that were added in the last 2 days.|
    |Today's job postings?|
    |What accounting positions are open in the london office?|
    |What positions are available for Senior Engineers?|
    |Where is the job listings|

    [![Screenshot dell'immissione di nuove espressioni per finalità MyStore](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Screenshot dell'immissione di nuove espressioni per finalità MyStore")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

    Fornendo _espressioni di esempio_, LUIS impara quali sono i tipi di espressione che dovranno essere stimate per la finalità in questione. 

    [!INCLUDE [Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    

## <a name="add-example-utterances-to-the-none-intent"></a>Aggiungere espressioni di esempio alla finalità None (Nessuna) 

[!INCLUDE [Follow these steps to add the None intent to the app](../../../includes/cognitive-services-luis-create-the-none-intent.md)]

## <a name="train-the-app-before-testing-or-publishing"></a>Eseguire il training dell'app prima del test o della pubblicazione

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-the-app-to-query-from-the-endpoint"></a>Pubblicare l'app per eseguire query dall'endpoint

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-from-the-endpoint"></a>Ottenere una stima della finalità dall'endpoint

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

1. Passare alla fine dell'URL nella barra degli indirizzi e immettere `I'm looking for a job with Natural Language Processing`. L'ultimo parametro della stringa di query è `q`, la **query** dell'espressione. Questa espressione non è uguale a nessuna delle espressioni di esempio. Si tratta di un test valido che dovrebbe restituire la finalità `GetJobInformation` come finalità con il punteggio più alto. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.9923871
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.9923871
        },
        {
          "intent": "None",
          "score": 0.007810574
        }
      ],
      "entities": []
    }
    ```

    Il parametro della stringa di query `verbose=true` indica di includere **tutte le finalità** nei risultati della query dell'app. La matrice delle entità è vuota perché questa app attualmente non dispone di entità. 

    Il risultato JSON identifica la finalità con il punteggio più alto come proprietà **`topScoringIntent`**. Tutti i punteggi sono compresi tra 1 e 0, il punteggio migliore è prossimo a 1. 

## <a name="create-intent-for-job-applications"></a>Creare una finalità per le candidature di lavoro

Tornare al portale LUIS e creare una nuova finalità per determinare se l'espressione dell'utente riguarda la candidatura per un lavoro.

1. Selezionare **Compilazione** dalla parte superiore, menu a destra per tornare alla compilazione delle applicazioni.

1. Selezionare **Intents** (Finalità) nel menu a sinistra per visualizzare l'elenco di finalità.

1. Selezionare **Crea nuova finalità** e immettere il nome `ApplyForJob`. 

    ![Finestra di dialogo di Language Understanding per creare nuove finalità](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

1. Aggiungere a questa finalità diverse espressioni che verranno presumibilmente usate dagli utenti, ad esempio:

    | Espressioni di esempio|
    |--|
    |Fill out application for job 123456|
    |Here is my c.v. for position 654234|
    |Here is my resume for the part-time receptionist post.|
    |I'm applying for the art desk job with this paperwork.|
    |I'm applying for the summer college internship in Research and Development in San Diego|
    |I'm requesting to submit my resume to the temporary position in the cafeteria.|
    |I'm submitting my resume for the new Autocar team in Columbus, OH|
    |I want to apply for the new accounting job.|
    |Job 456789 accounting internship paperwork is here|
    |Job 567890 and my paperwork|
    |My papers for the tulsa accounting internship are attached.|
    |My paperwork for the holiday delivery position|
    |Please send my resume for the new accounting job in seattle|
    |Submit resume for engineering position|
    |This is my c.v. for post 234123 in Tampa.|

    [![Screenshot dell'immissione di nuove espressioni per finalità ApplyForJob](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot dell'immissione di nuove espressioni per finalità ApplyForJob")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    La finalità etichettata viene evidenziata in rosso perché Language Understanding non è sicuro attualmente che la finalità è corretta. Il training dell'app indica a Language Understanding che le espressioni sono sulla finalità corretta. 

## <a name="train-again"></a>Eseguire nuovamente il training

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Pubblicare nuovamente

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-prediction-again"></a>Ottenere di nuovo la stima della finalità

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Nella nuova finestra del browser, immettere `Can I submit my resume for job 235986` alla fine dell'URL. 

    ```json
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9634406
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9634406
        },
        {
          "intent": "GetJobInformation",
          "score": 0.0171300638
        },
        {
          "intent": "None",
          "score": 0.00670867041
        }
      ],
      "entities": []
    }
    ```

    I risultati includono la nuova finalità **ApplyForJob**, nonché le finalità esistenti. 

## <a name="client-application-next-steps"></a>Passaggi successivi dell'applicazione client

Dopo che LUIS restituisce la risposta JSON, LUIS termina la richiesta. LUIS non risponde alle espressioni dell'utente, si limita a identificare il tipo di informazione richiesta in linguaggio naturale. Il follow-up discorsivo viene fornito dall'applicazione client, ad esempio il servizio Azure Bot. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="related-information"></a>Informazioni correlate

* [Tipi di entità](luis-concept-entity-types.md)
* [Come eseguire il training](luis-how-to-train.md)
* [Come eseguire la pubblicazione](luis-how-to-publish-app.md)
* [Come testare nel portale LUIS](luis-interactive-test.md)
* [Azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)


## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state create l'app Human Resources (HR) e 2 finalità, sono state aggiunte espressioni di esempio a ognuna di queste finalità e alla finalità None (Nessuna) e sono stati eseguiti il training, la pubblicazione e il test a livello dell'endpoint. Questi sono i passaggi di base per la creazione di un modello LUIS. 

Continuare con questa app [aggiungendo un'entità semplice e un elenco di frasi](luis-quickstart-primary-and-secondary-data.md).

> [!div class="nextstepaction"]
> [Aggiungere finalità ed entità predefinite a questa app](luis-tutorial-prebuilt-intents-entities.md)
