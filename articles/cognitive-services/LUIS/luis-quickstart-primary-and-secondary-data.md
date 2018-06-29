---
title: "Esercitazione: Creazione di un'app di Language Understanding per estrarre dati - Azure | Microsoft Docs"
description: Questa esercitazione illustra come creare una semplice app di Language Understanding usando finalità e un'entità semplice per estrarre dati di Machine Learning.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: v-geberr
ms.openlocfilehash: 1e8647e34da3d34946a4f6ac298017f6d4c99de6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265361"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>Esercitazione: Creare un'app che usa un'entità semplice
In questa esercitazione si crea un'app che illustra come estrarre dati di Machine Learning da un'espressione usando l'entità **semplice**.

<!-- green checkmark -->
> [!div class="checklist"]
> * Comprendere le entità semplici 
> * Creare una nuova app di Language Understanding per il settore della comunicazione con finalità SendMessage
> * Aggiungere la finalità _None_ (Nessuna) ed espressioni di esempio
> * Aggiungere un'entità semplice per estrarre il contenuto del messaggio dall'espressione
> * Eseguire il training e pubblicare l'app
> * Eseguire query sull'endpoint dell'app per ottenere la risposta JSON di Language Understanding

Per questo articolo è necessario un account gratuito di [Language Understanding][LUIS] per creare la propria applicazione.

## <a name="purpose-of-the-app"></a>Scopo dell'app
Questa app illustra come eseguire il pull dei dati da un'espressione. Si consideri l'espressione seguente di un chatbot:

```JSON
Send a message telling them to stop
```

La finalità è l'invio di un messaggio. I dati importanti dell'espressione sono il messaggio stesso, `telling them to stop`.  

## <a name="purpose-of-the-simple-entity"></a>Scopo dell'entità semplice
Lo scopo dell'entità semplice è insegnare a Language Understanding cos'è un messaggio e dove si trova in un'espressione. La parte dell'espressione che rappresenta il messaggio può variare da espressione ad espressione, a seconda della scelta delle parole e della lunghezza dell'espressione stessa. Language Understanding necessita di esempi di messaggi nelle espressioni di tutte le finalità.  

Per questa semplice app, il messaggio sarà al termine dell'espressione. 

## <a name="create-a-new-app"></a>Creare una nuova app
1. Accedere al sito Web di [Language Understanding][LUIS]. Accertarsi di accedere all'area in cui è necessario pubblicare gli endpoint di Language Understanding.

2. Sul sito Web di [Language Understanding][LUIS] selezionare **Create new app** (Crea nuova app).  

    ![Elenco di app di Language Understanding](./media/luis-quickstart-primary-and-secondary-data/app-list.png)

3. Nella finestra di dialogo popup, immettere il nome `MyCommunicator`. 

    ![Elenco di app di Language Understanding](./media/luis-quickstart-primary-and-secondary-data/create-new-app-dialog.png)

4. Al termine del processo, l'app visualizza la pagina **Intents** (Finalità) con la finalità **None** (Nessuna). 

    [![](media/luis-quickstart-primary-and-secondary-data/intents-list.png "Schermata della pagina Intents (Finalità) di Language Understanding con la finalità None (Nessuna)")](media/luis-quickstart-primary-and-secondary-data/intents-list.png#lightbox)

## <a name="create-a-new-intent"></a>Creare una nuova finalità

1. Nella pagina **Intents** (Finalità) selezionare **Create new intent** (Crea nuova finalità). 

    [![](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png "Schermata di Language Understanding con il pulsante 'Create new intent' (Crea nuova finalità) evidenziato")](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png#lightbox)

2. Immettere il nome della nuova finalità `SendMessage`. È necessario selezionare questa finalità ogni volta che un utente vuole inviare un messaggio.

    Creando una finalità, si crea la categoria principale di informazioni da identificare. Assegnando un nome alla categoria, qualsiasi altra applicazione che usi i risultati delle query di Language Understanding può usare il nome della categoria per trovare una risposta o intraprendere azioni appropriate. Language Understanding non risponde a queste domande, si limita a identificare il tipo di informazione richiesta in linguaggio naturale. 

    ![Immettere il nome finalità SendMessage](./media/luis-quickstart-primary-and-secondary-data/create-new-intent-popup-dialog.png)

3. Aggiungere alla finalità `SendMessage` sette espressioni che verranno presumibilmente usate dagli utenti, ad esempio:

    | Espressioni di esempio|
    |--|
    |Rispondi con "Ho avuto il tuo messaggio, avrò la risposta domani"|
    |Inviare il messaggio "Quando torni a casa?"|
    |Invia il messaggio "Sono occupato"|
    |Dire che deve essere fatto entro oggi ^^^|
    |Rispondi che sto guidando e risponderò più tardi|
    |Componi il messaggio "Quando è successo?" per Luca|
    |Saluta Fabrizio|

    [![](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png "Schermata di Language Understanding con le espressioni immesse")](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>Aggiungere espressioni alla finalità None (Nessuna)

L'app di Language Understanding non ha attualmente espressioni per la finalità **None** (Nessuna). Sono necessarie espressioni alle quali l'app non dovrà rispondere, quindi occorre inserire espressioni nella finalità **None** (Nessuna). Non lasciarla vuota. 
    
1. Selezionare **Intents** (Finalità) dal pannello di sinistra. 

    [![](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png "Schermata di Language Understanding con il pulsante 'Intents' (Finalità) evidenziato")](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png#lightbox)

2. Selezionare la finalità **None** (Nessuna). 

    [![](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png "Schermata di selezione della finalità None (Nessuna)")](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png#lightbox)

3. Aggiungere tre espressioni che l'utente potrebbe usare, ma che non sono pertinenti per l'app. Alcune espressioni **None** (Nessuna) valide sono le seguenti:

    | Espressioni di esempio|
    |--|
    |Annullare.|
    |Arrivederci|
    |Che succede?|
    
    Nell'applicazione chiamante di Language Understanding, ad esempio un chatbot, se Language Understanding restituisce la finalità **None** (Nessuna) per un'espressione, il bot può chiedere se l'utente intende terminare la conversazione. Il bot può anche dare altre indicazioni per proseguire la conversazione, se l'utente non intende terminarla. 

    [![](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png "Schermata di Language Understanding con espressioni per la finalità None (Nessuna)")](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png#lightbox)

## <a name="create-a-simple-entity-to-extract-message"></a>Creare un'entità semplice per estrarre messaggi 
1. Selezionare **Intents** (Finalità) dal menu a sinistra.

    ![Selezionare il collegamento Intents (Finalità)](./media/luis-quickstart-primary-and-secondary-data/select-intents-from-none-intent.png)

2. Selezionare `SendMessage` dall'elenco delle finalità.

    ![Selezionare la finalità SendMessage](./media/luis-quickstart-primary-and-secondary-data/select-sendmessage-intent.png)

3. Nell'espressione `Reply with I got your message, I will have the answer tomorrow`, selezionare la prima parola del corpo del messaggio, `I`, e l'ultima parola del corpo del messaggio, `tomorrow`. Tutte queste parole vengono selezionate per il messaggio e viene visualizzato un menu a discesa con una casella di testo in alto.

    [![](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png "Schermata di selezione delle parole nell'espressione per il messaggio")](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png#lightbox)

4. Immettere il nome dell'entità `Message` nella casella di testo.

    [![](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png "Schermata di immissione del nome dell'entità nella casella")](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png#lightbox)

5. Selezionare **Create new entity** (Crea nuova entità ) nel menu a discesa. Lo scopo dell'entità è estrarre il testo che costituisce il corpo del messaggio. In questa app di Language Understanding, il messaggio di testo si trova alla fine dell'espressione, ma l'espressione e il messaggio possono essere di qualsiasi lunghezza. 

    [![](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png "Schermata di creazione di una nuova entità dall'espressione")](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png#lightbox)

6. Nella finestra popup, il tipo di entità predefinito è **Simple** (Semplice) e il nome dell'entità è `Message`. Mantenere queste impostazioni e selezionare **Done** (Fine).

    ![Verificare il tipo di entità](./media/luis-quickstart-primary-and-secondary-data/entity-type.png)

7. Ora che l'entità è stata creata e un'espressione è stata etichettata, etichettare il resto delle espressioni con tale entità. Selezionare un'espressione, quindi la prima e l'ultima parola di un messaggio. Nel menu a discesa selezionare l'entità, `Message`. Il messaggio è ora etichettato nell'entità. Continuare a etichettare tutte le frasi del messaggio nelle espressioni rimanenti.

    [![](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png "Schermata con tutte le espressioni del messaggio etichettate")](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png#lightbox)

    La vista predefinita delle espressioni è la **vista entità**. Selezionare il controllo **Entities view** (Vista entità) sopra le espressioni. La **vista token** visualizza il testo delle espressioni. 

    [![](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png "Schermata delle espressioni nella vista token")](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png#lightbox)

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

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "Schermata della pagina Publish (Pubblica) con il pulsante Publish to production slot (Pubblica in slot di produzione) evidenziato")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. La pubblicazione è completata quando viene visualizzata la barra di stato verde nella parte superiore del sito Web che conferma il completamento.

## <a name="query-the-endpoint-with-a-different-utterance"></a>Eseguire una query sull'endpoint con un'espressione diversa
Nella pagina **Publish** (Pubblica) selezionare il collegamento all'**endpoint** nella parte inferiore della pagina. 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "Schermata della pagina Publish (Pubblica) con l'endpoint evidenziato")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

Questa azione apre un'altra finestra del browser con l'URL endpoint nella barra degli indirizzi. Passare alla fine dell'URL nell'indirizzo e immettere `text I'm driving and will be 30 minutes late to the meeting`. L'ultimo parametro querystring è `q`, la **query** dell'espressione. Questa espressione non corrisponde ad alcuna delle espressioni etichettate, per cui rappresenta un buon test e deve restituire le espressioni `SendMessage`.

```
{
  "query": "text I'm driving and will be 30 minutes late to the meeting",
  "topScoringIntent": {
    "intent": "SendMessage",
    "score": 0.987501
  },
  "intents": [
    {
      "intent": "SendMessage",
      "score": 0.987501
    },
    {
      "intent": "None",
      "score": 0.111048922
    }
  ],
  "entities": [
    {
      "entity": "i ' m driving and will be 30 minutes late to the meeting",
      "type": "Message",
      "startIndex": 5,
      "endIndex": 58,
      "score": 0.162995353
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>Quali attività ha eseguito l'app di Language Understanding?
Con solo due finalità e un'entità, questa app ha identificato una finalità di query in linguaggio naturale e ha restituito i dati del messaggio. 

Il risultato JSON identifica la finalità con il punteggio più alto `SendMessage` con un punteggio pari a 0.987501. Tutti i punteggi sono compresi tra 1 e 0, il punteggio migliore è prossimo a 1. Il punteggio della finalità `None` è 0.111048922, molto più vicino a zero. 

I dati del messaggio hanno un tipo, `Message`, nonché un valore, `i ' m driving and will be 30 minutes late to the meeting`. 

Il chatbot ha ora informazioni sufficienti per determinare l'azione principale, `SendMessage`, e un parametro di tale azione, il testo del messaggio. 

## <a name="where-is-this-luis-data-used"></a>Qual è la destinazione d'uso dei dati di Language Understanding? 
Language Understanding ha completato le attività relative alla richiesta. L'applicazione chiamante, ad esempio un chatbot, può acquisire il risultato topScoringIntent e i dati dall'entità per inviare il messaggio tramite un'API di terze parti. Se sono presenti altre opzioni a livello di codice per l'applicazione chiamante o il bot, Language Understanding non esegue tali operazioni, ma determina solo la finalità dell'utente. 

## <a name="clean-up-resources"></a>Pulire le risorse
Quando non è più necessaria, eliminare l'app di Language Understanding. A tale scopo, selezionare il menu con i puntini di sospensione (...) a destra del nome dell'app nell'elenco di app, quindi selezionare **Delete** (Elimina). Nella finestra di dialogo popup **Delete app?** (Eliminare l'app?) selezionare **OK**.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Informazioni su come aggiungere un'entità gerarchica](luis-quickstart-intent-and-hier-entity.md)


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
