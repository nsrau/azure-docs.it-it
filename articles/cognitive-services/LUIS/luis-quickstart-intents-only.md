---
title: Creare un'app semplice con due finalità - Azure | Microsoft Docs
description: Di seguito viene illustrato come creare una semplice app di Language Understanding usando due finalità e non impiegando entità per identificare le espressioni degli utenti in questa guida introduttiva.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: de295a93d395cee4c4dfbea4f2e7f7338036feb8
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2018
ms.locfileid: "39494374"
---
# <a name="tutorial-1-build-app-with-custom-domain"></a>Esercitazione: 1. Compilazione di app con dominio personalizzato
In questa esercitazione si impara a creare un'applicazione in grado di dimostrare come utilizzare le **finalità** per determinare le _intenzioni_ dell'utente in base all'espressione (testo) inviato all'applicazione. Al termine, sarà disponibile un endpoint Language Understanding in esecuzione nel cloud.

Questa app è il tipo più semplice di app Language Understanding perché non estrarre i dati dalle espressioni, ma determina solo la finalità dell'utente con l’espressione.

<!-- green checkmark -->
> [!div class="checklist"]
> * Creare una nuova app per un dominio di Risorse Umane (HR) 
> * Aggiungere finalità GetJobInformation
> * Aggiungere espressioni di esempio per la finalità GetJobInformation 
> * Eseguire il training e pubblicare l'app
> * Eseguire query sull'endpoint dell'app per ottenere una risposta JSON di Language Understanding
> * Aggiungere finalità ApplyForJob
> * Aggiungere espressioni di esempio per finalità ApplyForJob 
> * Eseguire il training, pubblicare ed eseguire di nuovo la query sull'endpoint 

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="purpose-of-the-app"></a>Scopo dell'app
Questa applicazione ha poche finalità. La prima finalità, **`GetJobInformation`**, identifica quando un utente desidera informazioni sui lavori disponibili all'interno di un'azienda. La seconda finalità, **`None`**, identifica ogni altro tipo di espressione. Più avanti nella Guida introduttiva viene aggiunta una terza finalità, `ApplyForJob`. 

## <a name="create-a-new-app"></a>Creare una nuova app
1. Accedere al sito Web di [Language Understanding](luis-reference-regions.md#luis-website). Accedere all'[area](luis-reference-regions.md#publishing-regions) in cui è necessario pubblicare gli endpoint di Language Understanding.

2. Sul sito Web di [Language Understanding](luis-reference-regions.md#luis-website) selezionare **Crea nuova app**.  

    [![](media/luis-quickstart-intents-only/app-list.png "Screenshot della pagina App personali")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. Nella finestra di dialogo popup, immettere il nome `HumanResources`. Questa applicazione risponde alle domande sul reparto Risorse Umane dell’azienda. Questo reparto si occupa di questioni relative all'occupazione, come le posizioni all'interno dell'azienda che devono essere occupate.

    ![Nuova app Language Understanding](./media/luis-quickstart-intents-only/create-app.png)

4. Al termine del processo, l'app visualizza la pagina **Intents** (Finalità) con la finalità **None** (Nessuna). 

## <a name="create-getjobinformation-intention"></a>Creare finalità GetJobInformation
1. Selezionare **Create new intent** (Crea nuova finalità). Immettere il nome della nuova finalità `GetJobInformation`. Questa finalità è prevista ogni volta che un utente desidera informazioni sulle posizioni aperte nella propria azienda.

    ![](media/luis-quickstart-intents-only/create-intent.png "Screenshot della finestra di dialogo di creazione di una nuova finalità")

    Creando una finalità, si crea la categoria di informazioni da identificare. Assegnando un nome alla categoria, qualsiasi altra applicazione che usi i risultati delle query di Language Understanding può usare il nome della categoria per trovare una risposta. Language Understanding non risponde a queste domande, si limita a identificare il tipo di informazione richiesta in linguaggio naturale. 

2. Aggiungere alla finalità sette espressioni che verranno presumibilmente usate dagli utenti, ad esempio:

    | Espressioni di esempio|
    |--|
    |Any new jobs posted today?|
    |What positions are available for Senior Engineers?|
    |Is there any work in databases?|
    |Looking for a new situation with responsibilities in accounting|
    |Where is the job listings|
    |New jobs?|
    |Are there any new positions in the Seattle office?|

    [![](media/luis-quickstart-intents-only/utterance-getstoreinfo.png "Schermata di immissione di nuove espressioni per la finalità MyStore")](media/luis-quickstart-intents-only/utterance-getstoreinfo.png#lightbox)

3. L'app di Language Understanding non ha attualmente espressioni per la finalità **None** (Nessuna). Sono necessarie espressioni a cui l'app non risponde. Non lasciarla vuota. Selezionare **Intents** (Finalità) dal pannello di sinistra. 

4. Selezionare la finalità **None** (Nessuna). Aggiungere tre espressioni che l'utente potrebbe usare, ma che non sono pertinenti per l'app. Se l'applicazione riguarda gli annunci di lavoro, alcune valide espressioni **None** (Nessuna) sono:

    | Espressioni di esempio|
    |--|
    |Barking dogs are annoying|
    |Order a pizza for me|
    |Penguins in the ocean|

    Nell'applicazione che effettua la chiamata di Language Understanding, ad esempio un chatbot, se Language Understanding restituisce la finalità **None** (Nessuna) per un'espressione, il bot può chiedere se l'utente intende terminare la conversazione. Il chatbot può anche dare altre indicazioni per proseguire la conversazione, se l'utente non intende terminarla. 

## <a name="train-and-publish-the-app"></a>Eseguire il training dell'app e pubblicarla

[!include[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-app-to-endpoint"></a>Pubblicare l'app su endpoint

[!include[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="query-endpoint-for-getjobinformation-intent"></a>Endpoint di query per finalità GetJobInformation

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Andare alla fine dell'URL nell'indirizzo e immettere `I'm looking for a job with Natual Language Processing`. L'ultimo parametro della stringa di query è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni di esempio del passaggio 4, per cui rappresenta un buon test e deve restituire la finalità `GetJobInformation` come finalità con il punteggio più alto. 

    ```
    {
      "query": "I'm looking for a job with Natual Language Processing",
      "topScoringIntent": {
        "intent": "GetJobInformation",
        "score": 0.8965092
      },
      "intents": [
        {
          "intent": "GetJobInformation",
          "score": 0.8965092
        },
        {
          "intent": "None",
          "score": 0.147104025
        }
      ],
      "entities": []
    }
    ```

## <a name="create-applyforjob-intention"></a>Creare finalità ApplyForJob
Tornare alla scheda del browser del sito Language Understanding e creare una nuova finalità di candidatura per un lavoro.

1. Selezionare **Compilazione** dalla parte superiore, menu a destra per tornare alla compilazione delle applicazioni.

2. Selezionare **Intents** (Finalità) dal menu a sinistra.

3. Selezionare **Crea nuova finalità** e immettere il nome `ApplyForJob`. 

    ![Finestra di dialogo di Language Understanding per creare nuove finalità](./media/luis-quickstart-intents-only/create-applyforjob-intent.png)

4. Aggiungere a questa finalità diverse espressioni che verranno presumibilmente usate dagli utenti, ad esempio:

    | Espressioni di esempio|
    |--|
    |I want to apply for the new accounting job.|
    |Fill out application for job 123456|
    |Submit resume for engineering position|
    |Here is my c.v. for position 654234|
    |Job 567890 and my paperwork|

    [![](media/luis-quickstart-intents-only/utterance-applyforjob.png "Screenshot di immissione di nuove espressioni per la finalità ApplyForJob")](media/luis-quickstart-intents-only/utterance-applyforjob.png#lightbox)

    La finalità etichettata viene evidenziata in rosso perché Language Understanding non è sicuro attualmente che la finalità è corretta. Il training dell'app indica a Language Understanding che le espressioni sono sulla finalità corretta. 

    [Eseguire il training e pubblicare](#train-and-publish-the-app) di nuovo 

## <a name="query-endpoint-for-applyforjob-intent"></a>Endpoint di query per finalità ApplyForJob

1. [!include[LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Nella nuova finestra del browser, immettere `Can I submit my resume for job 235986` alla fine dell'URL. 

    ```
    {
      "query": "Can I submit my resume for job 235986",
      "topScoringIntent": {
        "intent": "ApplyForJob",
        "score": 0.9166808
      },
      "intents": [
        {
          "intent": "ApplyForJob",
          "score": 0.9166808
        },
        {
          "intent": "GetJobInformation",
          "score": 0.07162977
        },
        {
          "intent": "None",
          "score": 0.0262826588
        }
      ],
      "entities": []
    }
    ```

## <a name="what-has-this-luis-app-accomplished"></a>Quali attività ha eseguito l'app di Language Understanding?
Questa applicazione, con poche finalità, ha identificato una query di linguaggio naturale che è relativa alla stessa finalità ma formulata in modo diverso. 

Il risultato JSON identifica la finalità con il punteggio più alto. Tutti i punteggi sono compresi tra 1 e 0, il punteggio migliore è prossimo a 1. Il punteggio `GetJobInformation` e `None` delle finalità è molto più vicino allo zero. 

## <a name="where-is-this-luis-data-used"></a>Qual è la destinazione d'uso dei dati di Language Understanding? 
Language Understanding ha completato le attività relative alla richiesta. L'applicazione chiamante, come una chatbot, può prendere il risultato topScoringIntent e trovare informazioni (non memorizzate in LUIS) per rispondere alla domanda o terminare la conversazione. Queste sono opzioni programmatiche per il bot o per l'applicazione chiamante. Language Understanding non esegue queste operazioni, ma si limita a determinare l'intenzione dell'utente. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Aggiungere finalità ed entità predefinite a questa app](luis-tutorial-prebuilt-intents-entities.md)
