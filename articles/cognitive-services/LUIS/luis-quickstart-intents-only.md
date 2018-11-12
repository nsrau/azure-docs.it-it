---
title: "Esercitazione 1: trovare intenzioni nell'app LUIS personalizzata"
titleSuffix: Azure Cognitive Services
description: Creare un'app personalizzata che stima l'intenzione dell'utente. Questa app è il tipo più semplice di app LUIS perché non estrae i vari elementi di dati dal testo dell'espressione, come gli indirizzi di posta elettronica o le date.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: tutorial
ms.date: 09/09/2018
ms.author: diberry
ms.openlocfilehash: cc631f139e7f11d82f2caac83770754060353d07
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/08/2018
ms.locfileid: "51277716"
---
# <a name="tutorial-1-build-custom-app-to-determine-user-intentions"></a>Esercitazione 1: creare un'app personalizzata per determinare le intenzioni dell'utente

In questa esercitazione verrà creata un'app Human Resources (HR) personalizzata che consente di prevedere l'intenzione dell'utente in base all'espressione (testo). Al termine, sarà disponibile un endpoint Language Understanding in esecuzione nel cloud.

Lo scopo dell'app è di determinare l'intenzione della conversazione di un testo discorsivo e con linguaggio naturale. Queste intenzioni sono suddivise in **Finalità**. Questa applicazione ha poche finalità. La prima finalità, **`GetJobInformation`**, identifica quando un utente desidera informazioni sui lavori disponibili all'interno di un'azienda. La seconda finalità, **`None`**, viene usata per le espressioni provenienti dall'utente che non rientrano nel _dominio_ (ambito) di questa app. Successivamente, una terza finalità, **`ApplyForJob`**, sarà aggiunta per le espressioni riguardanti la candidatura per un lavoro. Questa terza finalità è diversa da `GetJobInformation` perché le informazioni sul lavoro dovrebbero essere già note quando un utente presenta la propria candidatura per il lavoro stesso. Tuttavia, a seconda della scelta di parole, determinare la finalità potrebbe risultare difficile poiché entrambe fanno riferimento a un lavoro.

Dopo che LUIS restituisce la risposta JSON, LUIS termina la richiesta. LUIS non risponde alle espressioni dell'utente, si limita a identificare il tipo di informazione richiesta in linguaggio naturale. 

**In questa esercitazione si imparerà come:**

> [!div class="checklist"]
> * Creare una nuova app 
> * Creare finalità
> * Aggiungere espressioni di esempio
> * Eseguire il training dell'app
> * Pubblicare l'app
> * Ottenere finalità dall'endpoint

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="create-a-new-app"></a>Creare una nuova app

1. Accedere al portale LUIS con l'URL [https://www.luis.ai](https://www.luis.ai). 

2. Selezionare **Create new app** (Crea nuova app).  

    [![](media/luis-quickstart-intents-only/app-list.png "Screenshot della pagina My Apps del sito LUIS (Language Understanding)")](media/luis-quickstart-intents-only/app-list.png#lightbox)

3. Nella finestra di dialogo popup, immettere il nome `HumanResources` e non modificare le impostazioni cultura predefinite (**English**). Lasciare vuota la descrizione.

    ![Nuova app Language Understanding](./media/luis-quickstart-intents-only/create-app.png)

    Successivamente, l'app visualizza la pagina **Intents** (Finalità) con la finalità **None** (Nessuna).

## <a name="getjobinformation-intent"></a>Finalità GetJobInformation

1. Selezionare **Create new intent** (Crea nuova finalità). Immettere il nome della nuova finalità `GetJobInformation`. Questa finalità è prevista ogni volta che un utente desidera informazioni sulle mansioni aperte all'interno dell'azienda.

    ![](media/luis-quickstart-intents-only/create-intent.png "Screenshot della finestra di dialogo New intent del sito LUIS (Language Understanding)")

2. Fornendo _espressioni di esempio_, LUIS impara quali sono i tipi di espressione che dovranno essere stimate per la finalità in questione. Aggiungere a questa finalità diverse espressioni di esempio che verranno presumibilmente usate dagli utenti, ad esempio:

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

    [!INCLUDE[Do not use too few utterances](../../../includes/cognitive-services-luis-too-few-example-utterances.md)]    


## <a name="none-intent"></a>Finalità None 
L'applicazione client deve sapere se un'espressione non rientra nel dominio soggetto dell'applicazione. Se LUIS restituisce la finalità **None** (Nessuna) per un'espressione, l'applicazione client può chiedere se l'utente intende terminare la conversazione. L'applicazione client può anche dare altre indicazioni per proseguire la conversazione, se l'utente non intende terminarla. 

Queste espressioni di esempio, che non rientrano nel dominio soggetto, sono raggruppate nella finalità **None** (Nessuna). Non lasciarla vuota. 

1. Selezionare **Intents** (Finalità) dal pannello di sinistra.

2. Selezionare la finalità **None** (Nessuna). Aggiungere tre espressioni che l'utente potrebbe usare, ma che non sono pertinenti per l'app Human Resources. Se l'applicazione riguarda gli annunci di lavoro, alcune espressioni **None** (Nessuna) sono:

    | Espressioni di esempio|
    |--|
    |Barking dogs are annoying|
    |Order a pizza for me|
    |Penguins in the ocean|


## <a name="train"></a>Eseguire il training 

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish"></a>Pubblica

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent"></a>Ottenere la finalità

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Passare alla fine dell'URL nella barra degli indirizzi e immettere `I'm looking for a job with Natural Language Processing`. L'ultimo parametro della stringa di query è `q`, la **query** dell'espressione. Questa espressione non è uguale a nessuna delle espressioni di esempio. Si tratta di un test valido che dovrebbe restituire la finalità `GetJobInformation` come finalità con il punteggio più alto. 

    ```JSON
    {
      "query": "I'm looking for a job with Natural Language Processing",
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

    I risultati includono **tutte le finalità** nell'app, attualmente 2. La matrice delle entità è vuota perché questa app attualmente non dispone di entità. 

    Il risultato JSON identifica la finalità con il punteggio più alto come proprietà **`topScoringIntent`**. Tutti i punteggi sono compresi tra 1 e 0, il punteggio migliore è prossimo a 1. 

## <a name="applyforjob-intent"></a>Finalità ApplyForJob
Tornare al sito Web LUIS e creare una nuova finalità per determinare se l'espressione dell'utente riguarda la candidatura per un lavoro.

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

## <a name="train-again"></a>Eseguire nuovamente il training

[!INCLUDE[LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="publish-again"></a>Pubblicare nuovamente

[!INCLUDE[LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)] 

## <a name="get-intent-again"></a>Ottenere nuovamente la finalità

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)]

2. Nella nuova finestra del browser, immettere `Can I submit my resume for job 235986` alla fine dell'URL. 

    ```JSON
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

    I risultati includono la nuova finalità **ApplyForJob**, nonché le finalità esistenti. 

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state create l'app Human Resources (HR) e 2 finalità, sono state aggiunte espressioni di esempio a ognuna di queste finalità e alla finalità None (Nessuna) e sono stati eseguiti il training, la pubblicazione e il test a livello dell'endpoint. Questi sono i passaggi di base per la creazione di un modello LUIS. 

> [!div class="nextstepaction"]
> [Aggiungere finalità ed entità predefinite a questa app](luis-tutorial-prebuilt-intents-entities.md)
