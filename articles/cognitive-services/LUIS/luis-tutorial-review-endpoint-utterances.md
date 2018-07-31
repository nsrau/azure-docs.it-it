---
title: Esercitazione per esaminare le espressioni endpoint in LUIS (Language Understanding) - Azure | Microsoft Docs
description: In questa esercitazione viene illustrato come esaminare le espressioni endpoint nel dominio relativo alle risorse umane (HR) in LUIS.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 07/03/2018
ms.author: diberry
ms.openlocfilehash: 1f1e3310e0d02983aaecc3f87ba9c116d65b751b
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237214"
---
# <a name="tutorial-review-endpoint-utterances"></a>Esercitazione: Esaminare le espressioni endpoint
In questa esercitazione è possibile migliorare le previsioni delle app tramite la verifica o la correzione delle espressioni ricevute tramite l'endpoint HTTP del servizio LUIS. 

<!-- green checkmark -->
> [!div class="checklist"]
> * Informazioni sull'analisi delle espressioni endpoint 
> * Usare un'app LUIS per il dominio delle risorse umane 
> * Esaminare le espressioni endpoint
> * Eseguire il training e pubblicare l'app
> * Eseguire query sull'endpoint dell'app per ottenere una risposta JSON di Language Understanding

Per questo articolo è necessario un account [LUIS](luis-reference-regions.md#luis-website) gratuito per creare un'applicazione LUIS personalizzata.

## <a name="before-you-begin"></a>Prima di iniziare
Se non è disponibile l'app relativa alle risorse umane creata nell'esercitazione sul [sentiment](luis-quickstart-intent-and-sentiment-analysis.md), importare l'app dal repository [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-sentiment-HumanResources.json) di Github. Se si usa questa esercitazione come nuova app importata, è inoltre necessario eseguire il training, pubblicare e quindi aggiungere le espressioni all'endpoint tramite uno [script](https://github.com/Microsoft/LUIS-Samples/blob/master/examples/demo-upload-endpoint-utterances/endpoint.js) o dall'endpoint in un browser. Le espressioni da aggiungere sono le seguenti:

   [!code-nodejs[Node.js code showing endpoint utterances to add](~/samples-luis/examples/demo-upload-endpoint-utterances/endpoint.js?range=15-26)]

Se si vuole mantenere l'app originale, clonare la versione nella pagina [Settings](luis-how-to-manage-versions.md#clone-a-version) (Impostazioni) assegnando il nome `review`. La clonazione è un ottimo modo per provare le diverse funzionalità di LUIS senza modificare la versione originale. 

Se sono disponibili tutte le versioni dell'app, tramite la serie di esercitazioni, potrebbe risultare sorprendente il fatto che l'elenco **Review endpoint utterances** (Esamina espressioni endpoint) non subisce alcuna modifica in base alla versione. È disponibile un singolo pool di espressioni da esaminare, indipendentemente dalla versione dell'espressione che si sta modificando attivamente o dalla versione dell'app pubblicata nell'endpoint. 

## <a name="purpose-of-reviewing-endpoint-utterances"></a>Finalità della revisione delle espressioni endpoint
Questo processo di revisione è un altro approccio usato da LUIS per conoscere il dominio dell'app. LUIS ha selezionato le espressioni nell'elenco di revisione. L'elenco ha le caratteristiche seguenti:

* È specifico per l'app.
* È progettato per migliorare la precisione delle previsioni dell'app. 
* Deve essere esaminato periodicamente. 

La revisione delle espressioni endpoint consente di verificare o correggere la finalità prevista dell'espressione. Consente anche di etichettare entità personalizzate non previste. 

## <a name="review-endpoint-utterances"></a>Esaminare le espressioni endpoint

1. Assicurarsi che l'app relativa alle risorse umane sia presente nella sezione **Build** (Compila) di LUIS. È possibile passare a questa sezione selezionando **Build** nella barra dei menu in alto a destra. 

    [ ![Screenshot dell'app LUIS con il pulsante relativo alla compilazione evidenziato in alto a destra sulla barra di spostamento](./media/luis-tutorial-review-endpoint-utterances/first-image.png)](./media/luis-tutorial-review-endpoint-utterances/first-image.png#lightbox)

1. Selezionare **Review endpoint utterances** (Esamina espressioni endpoint) dal riquadro di spostamento a sinistra. All'elenco viene applicato un filtro relativo alla finalità **ApplyForJob**. 

    [ ![Screenshot dell'elenco di revisione delle espressioni endpoint nel riquadro di spostamento a sinistra](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png)](./media/luis-tutorial-review-endpoint-utterances/entities-view-endpoint-utterances.png#lightbox)

2. Attivare/disattivare la **Entities view** (Visualizzazione entità) per visualizzare le entità con etichette. 
    
    [ ![Screenshot dell'elenco di revisione delle espressioni endpoint con attivazione/disattivazione della visualizzazione entità evidenziata](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png)](./media/luis-tutorial-review-endpoint-utterances/select-entities-view.png#lightbox)

    |Espressione|Finalità corretta|Entità mancanti|
    |:--|:--|:--|
    |I'm looking for a job with Natural Language Processing|GetJobInfo|Job - "Natural Language Process"|

    Questa espressione non ha la finalità corretta e ha un punteggio inferiore al 50%. La finalità **ApplyForJob** è associata a 21 espressioni rispetto alle sette espressioni in **GetJobInformation**. Oltre ad allineare correttamente l'espressione endpoint, è necessario aggiungere altre espressioni alla finalità **GetJobInformation**. Questo è un esercizio che deve essere completato autonomamente dall'utente. Ogni finalità, ad eccezione della finalità **None**, deve avere approssimativamente lo stesso numero di espressioni di esempio. La finalità **None** deve avere il 10% delle espressioni totali nell'app. 

    Quando è attiva l'opzione **Tokens View** (Visualizzazione token), è possibile passare il puntatore su qualsiasi testo blu nell'espressione per visualizzare il nome dell'entità prevista. 

3. Per la finalità `I'm looking for a job with Natual Language Processing` selezionare la finalità corretta, **GetJobInformation** nella colonna **Aligned intent** (Finalità allineata). 

    [ ![Screenshot della revisione delle espressioni endpoint per l'allineamento dell'espressione alla finalità](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png)](./media/luis-tutorial-review-endpoint-utterances/align-intent-1.png#lightbox)

4. Nella stessa espressione l'entità per `Natural Language Processing` è keyPhrase. Dovrebbe trattarsi invece ci un'entità **Job**. Selezionare `Natural Language Processing` e quindi selezionare l'entità **Job** dall'elenco.

    [ ![Screenshot della revisione delle espressioni endpoint per l'applicazione di etichette a un'entità nell'espressione](./media/luis-tutorial-review-endpoint-utterances/label-entity.png)](./media/luis-tutorial-review-endpoint-utterances/label-entity.png#lightbox)

5. Nella stessa riga selezionare il segno di spunta racchiuso in un cerchio nella colonna **Add to aligned intent** (Aggiungi alla finalità allineata). 

    [ ![Screenshot della finalizzazione dell'allineamento delle espressioni nella finalità](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png)](./media/luis-tutorial-review-endpoint-utterances/align-utterance.png#lightbox)

    Questa azione sposta l'espressione da **Review endpoint utterances** (Esamina espressioni endpoint) alla finalità **GetJobInformation**. L'espressione endpoint è ora un'espressione di esempio per tale finalità. 

6. Esaminare le espressioni rimanenti in questa finalità, assegnando etichette alle espressioni e correggendo il valore di **Aligned intent** (Finalità allineata), in caso di errori.

7. Quando tutte le espressioni sono corrette, selezionare la casella di controllo in ogni riga e quindi selezionare **Add selected** (Aggiungi elementi selezionati) per allineare correttamente le espressioni. 

    [ ![Screenshot della finalizzazione delle espressioni rimanenti per la finalità allineata](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png)](./media/luis-tutorial-review-endpoint-utterances/finalize-utterance-alignment.png#lightbox)

8. Queste espressioni non dovrebbero essere più incluse nell'elenco. Se vengono visualizzate altre espressioni, continuare a esaminare l'elenco, correggendo le finalità e assegnando etichette a eventuali entità mancanti, fino allo svuotamento dell'elenco. Selezionare la finalità successiva nell'elenco di filtri, quindi continuare a correggere le espressioni e ad assegnare etichette alle entità. Occorre ricordare che l'ultimo passaggio per ogni finalità consiste nel selezionare **Add to aligned intent** (Aggiungi alla finalità allineata) nella riga dell'espressione o nel selezionare la casella corrispondente a ogni finalità e quindi selezionare **Add selected** (Aggiungi elementi selezionati) sopra la tabella. 

    Questa app è molto piccola. Il processo di revisione richiede solo qualche minuto.

## <a name="add-new-job-name-to-phrase-list"></a>Aggiungere un nuovo nome di lavoro all'elenco di frasi
Mantenere aggiornato l'elenco di frasi inserendo eventuali nomi di lavoro individuati di recente. 

1. Selezionare **Phrase lists** (Elenchi di frasi) dal riquadro di spostamento sinistro.

2. Selezionare l'elenco di frasi **Jobs**.

3. Aggiungere `Natural Language Processing` come valore e quindi selezionare **Save** (Salva). 

## <a name="train-the-luis-app"></a>Eseguire il training dell'app di Language Understanding
Language Understanding non riconosce le modifiche fino a quando non è stato eseguito il training. 

1. Nella parte superiore destra del sito Web LUIS selezionare il pulsante **Train** (Esegui il training).

2. Il training è completato quando nella barra di stato verde nella parte superiore del sito Web viene confermato il completamento.

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Pubblicare l'app per ottenere l'URL endpoint
Per ottenere il modello aggiornato dell'app LUIS in un chatbot o in un'altra applicazione, è necessario pubblicare l'app. 

1. Nella parte superiore destra del sito Web LUIS selezionare il pulsante **Publish** (Pubblica). 

2. Se questa app è stata importata, è necessario selezionare **Sentiment analysis** (Analisi del sentiment). 

3. Selezionare lo slot di produzione, quindi fare clic sul pulsante **Publish** (Pubblica).

4. La pubblicazione è completata quando la barra di stato verde nella parte superiore del sito Web conferma il completamento.

## <a name="query-the-endpoint-with-an-utterance"></a>Eseguire la query dell'endpoint con un'espressione
Provare a usare un'espressione simile all'espressione corretta. 

1. Nella pagina **Publish** (Pubblica) selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. Questa azione apre un'altra finestra del browser con l'URL dell'endpoint nella barra degli indirizzi. 

2. Passare alla fine dell'URL nell'indirizzo e immettere `Are there any natural language processing jobs in my department right now?`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. 

```JSON
{
  "query": "are there any natural language processing jobs in my department right now?",
  "topScoringIntent": {
    "intent": "GetJobInformation",
    "score": 0.9247605
  },
  "intents": [
    {
      "intent": "GetJobInformation",
      "score": 0.9247605
    },
    {
      "intent": "ApplyForJob",
      "score": 0.129989788
    },
    {
      "intent": "FindForm",
      "score": 0.006438211
    },
    {
      "intent": "EmployeeFeedback",
      "score": 0.00408575451
    },
    {
      "intent": "Utilities.StartOver",
      "score": 0.00194211153
    },
    {
      "intent": "None",
      "score": 0.00166400627
    },
    {
      "intent": "Utilities.Help",
      "score": 0.00118593348
    },
    {
      "intent": "MoveEmployee",
      "score": 0.0007885918
    },
    {
      "intent": "Utilities.Cancel",
      "score": 0.0006373631
    },
    {
      "intent": "Utilities.Stop",
      "score": 0.0005980781
    },
    {
      "intent": "Utilities.Confirm",
      "score": 3.719905E-05
    }
  ],
  "entities": [
    {
      "entity": "right now",
      "type": "builtin.datetimeV2.datetime",
      "startIndex": 64,
      "endIndex": 72,
      "resolution": {
        "values": [
          {
            "timex": "PRESENT_REF",
            "type": "datetime",
            "value": "2018-07-05 15:23:18"
          }
        ]
      }
    },
    {
      "entity": "natural language processing",
      "type": "Job",
      "startIndex": 14,
      "endIndex": 40,
      "score": 0.9869922
    },
    {
      "entity": "natural language processing jobs",
      "type": "builtin.keyPhrase",
      "startIndex": 14,
      "endIndex": 45
    },
    {
      "entity": "department",
      "type": "builtin.keyPhrase",
      "startIndex": 53,
      "endIndex": 62
    }
  ],
  "sentimentAnalysis": {
    "label": "positive",
    "score": 0.8251864
  }
}
}
```

La finalità corretta è stata prevista con un punteggio elevato e l'entità **Job** viene rilevata come `natural language processing`. 

## <a name="can-reviewing-be-replaced-by-adding-more-utterances"></a>Possibilità di sostituire la revisione tramite l'aggiunta di altre espressioni 
È possibile che ci si domandi se non sia possibile aggiungere altre espressioni di esempio e quale sia la finalità della revisione delle espressioni endpoint. In un'app LUIS concreta le espressioni endpoint vengono generate da utenti con scelte di parole e strutture di frase non ancora usate. Se fossero state usate le stesse scelte di parole e le stesse strutture, la previsione originale avrebbe avuto una percentuale maggiore. 

## <a name="why-is-the-top-intent-on-the-utterance-list"></a>Ragioni per la presenza della finalità principale nell'elenco di espressioni 
Alcune espressioni endpoint presenteranno una percentuale elevata nell'elenco di revisione. È comunque necessario esaminare e verificare tali espressioni. Sono incluse nell'elenco perché la finalità con punteggio più elevato successiva ha un punteggio troppo vicino a quello della finalità principale. 

## <a name="what-has-this-tutorial-accomplished"></a>Risultati di questa esercitazione
La precisione delle previsioni dell'app è aumentata grazie alla revisione delle espressioni dall'endpoint. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app LUIS. Seleziona **App personali** nel menu in alto a sinistra. Selezionare i puntini di sospensione **...** a destra del nome dell'app nell'elenco di app e quindi selezionare **Delete** (Elimina). Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni sull'uso dei criteri](luis-tutorial-pattern.md)
